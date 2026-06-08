# Especificação — Usuários, Perfis e Permissões

> Repositório: **EspaçoN** (produto) · herda regras do template `../template`.
> Caminho sugerido: `docs/specifications/users/users-and-permissions.md`
> Status: **Rascunho v2 — regras confirmadas; frontend lidera o contrato (mock-first)**

---

## Nome

Cadastro e manutenção de **Usuários**, **Perfis de Usuário** e **Permissões de acesso**.

---

## Objetivo

Cadastrar e manter usuários, atribuir permissões granulares por **recurso/ação**,
e carregar dados de acesso (operador de caixa, remoto, restrição de horário/IP)
que módulos do ERP consomem. Tudo simples de operar e preparado para crescer.

A permissão é **transversal**: o mesmo catálogo alimenta o menu lateral, os
guards de rota e a liberação de qualquer botão/tela/feature do sistema.

---

## Contexto

EspaçoN é multiempresa (matriz + franquias) e é a base de produtos de gestão
(ERP) da Noturno. O acesso é sempre **individual por usuário**; o perfil é apenas
um **modelo** para acelerar o cadastro, **nunca** fonte de autorização.

Detalha e embasa o **ADR-006 (permissões)**.

---

## Princípios inegociáveis

1. **Perfil não concede acesso.** Selecionar perfil apenas **copia** ações para o
   usuário. A autorização lê sempre `user.permissions`.
2. **Acesso é por usuário e por (recurso, ação).** Cada recurso tem 9 flags.
3. **Catálogo ≠ menu lateral.** O catálogo é a lista de **todos os recursos** do
   ERP (telas, botões, features). Nem todo recurso é item de menu.
4. **Frontend lidera o contrato.** Backend não existe; será feito sob medida por
   módulo depois. O **mock do frontend é a fonte da verdade**; a camada `data`
   isola a conversão futura (mapper).
5. **Frontend modela e exibe; backend será a autoridade de segurança.** Horário e
   IP, em produção, são aplicados também no backend.
6. **Mock-first.**

---

## Regras de Negócio

### Usuário

* **Vínculo com funcionário (opcional):** nem todo usuário é funcionário
  (`employeeId` opcional).
* **Operador de caixa (dado de usuário, consumido por módulos — visão futura):**
  * `isOperator` (true/false); se operador → `type` = `unlimited | limited`;
    se `limited` → exige `operatorCode`.
  * **Não é portão do checker.** Módulos financeiros que precisem tratam:
    * `limited` → operação travada no `operatorCode` do usuário;
    * `unlimited` → pode escolher/filtrar/ver dados de qualquer operador.
  * Ex.: "Lançamento de saída de caixa" exige indicar um operador → limitado já
    vem travado; ilimitado escolhe.
* **`remote` (flag — visão futura):** habilita features que **atravessam
  empresas**. Ex.: logado na loja A, consultar débitos de cliente da loja B.
  Só é avaliado quando a operação for cross-company.

### Restrições opcionais (avaliadas no login e a cada ação)

* **Horário (`accessTimeRestriction`, opcional):** quando presente, a ação só
  passa dentro da janela. Avaliada **no login** e **a cada ação** (ex.: encerra
  18:00 → lançar caixa às 18:10 é bloqueado).
* **IP público (`ipRestriction`, opcional):** quando presente, o IP deve estar na
  lista. Frontend reflete/edita; aplicação real é do backend.

### Permissão (recurso)

Composta por: `código`, `descrição` e 9 ações booleanas — `abrir`, `buscar`,
`novo`, `modificar`, `apagar`, `imprimir`, `relatório`, `gráfico`, `remoto`.

* **Correspondência por descrição:** a ação é resolvida pela `descrição`. O
  backend deve **padronizar** (MAIÚSCULAS, sem acento). O frontend normaliza
  defensivamente (`shared/extensions`) para casar com segurança.
* **Catálogo:** lista mestra de todos os recursos do ERP, agrupados por **sessão**
  (para a matriz). Itens de menu e não-menu convivem (ex.: "Desbloquear Cliente",
  ação `novo`; "Contrato", ações `abrir`/`imprimir`).

### Regra do "remoto" (dois portões — cross-company, futuro)

Para a ação `A` no recurso `R`:
1. **Base:** `user.permissions[R].actions[A] === true`, senão **nega**.
2. **Se contexto remoto:** exige `user.remote === true` **e**
   `user.permissions[R].actions.remote === true`.
3. **Se houver restrição de horário/IP:** validar.

### Perfil

* Modelo com `permissions`. Ao selecioná-lo no usuário, **redefine** todas as
  ações conforme o perfil (com confirmação — sobrescreve). Depois, o usuário
  edita livremente. **Nunca** é consultado na autorização.

---

## Como o resto do sistema consome (padrão declarativo)

Qualquer parte do app declara o que precisa; o checker resolve sobre o usuário
em sessão:

```txt
Sidebar item     → can('CADASTRO DE CLIENTE', 'abrir')
Botão liberar    → can('DESBLOQUEAR CLIENTE', 'novo')
Ver contrato     → can('CONTRATO', 'abrir')  /  imprimir → can('CONTRATO', 'imprimir')
Guard de rota    → can(<recurso da rota>, 'abrir')
```

Glue de presentation em `shared/access`: composable `useAccess()` e diretiva
`v-can="{ key, action }"` (esconde/desabilita).

---

## Fluxo

### Autorização (runtime — síncrono e puro)

```txt
Componente/Guard/Sidebar
→ AccessChecker.can({ permissionKey, action, remoteContext?, now? })
→ aplica portões sobre user.permissions já carregadas
→ { allowed } | { allowed:false, reason }
```

### CRUD (assíncrono)

```txt
Page → UsersStore (BaseCrudStore) → UseCase → Repository → Provider (Mock) → AsyncResult<T>
```

---

## Entradas / Saídas

* **Entradas:** dados do usuário (identificação, vínculo, caixa, remoto,
  restrições, permissões); dados do perfil; catálogo de recursos.
* **Saídas:** usuário/perfil persistido; listas com filtro/ordenação/paginação;
  resultado de autorização.

---

## Casos de Erro

`DenyReason`: `NO_PERMISSION`, `REMOTE_NOT_ALLOWED_FOR_USER`,
`REMOTE_NOT_ALLOWED_FOR_RESOURCE`, `OUTSIDE_ALLOWED_TIME`, `IP_NOT_ALLOWED`.
CRUD: validação, conflito, não encontrado, rede — sempre via `AsyncResult`/`AppError`.

---

## Contratos

> **Frontend lidera.** Os models e o mock abaixo são a fonte da verdade. O
> backend de cada módulo será feito sob medida e a conversão viverá em
> `data/providers`/mappers. Models em inglês (padrão do template).

```ts
// domain/enums/permission-action.ts
export enum PermissionAction {
  Open = 'open', Search = 'search', Create = 'create', Update = 'update',
  Delete = 'delete', Print = 'print', Report = 'report', Chart = 'chart',
  Remote = 'remote',
}
export type PermissionActions = Record<PermissionAction, boolean>;

// domain/models/permission.ts — um RECURSO permissionável (tela/botão/feature)
export type Permission = {
  code: string;                       // código
  key: string;                        // descrição normalizada (UPPER, sem acento)
  label: string;                      // descrição amigável
  section: string;                    // agrupamento (sessão) p/ a matriz
  actions: PermissionActions;
};

// domain/models/permission-catalog.ts — lista mestra do ERP
export type PermissionCatalogEntry = {
  code: string;
  key: string;
  label: string;
  section: string;
  supportedActions?: PermissionAction[]; // dica de UI; ausente = todas as 9
};

// domain/enums/cash-operator-type.ts
export enum CashOperatorType { Unlimited = 'unlimited', Limited = 'limited' }

// domain/models/cash-operator.ts
export type CashOperator = {
  isOperator: boolean;
  type: CashOperatorType | null;
  operatorCode: string | null;        // obrigatório quando Limited
};

// domain/models/access-restrictions.ts
export type AccessTimeRestriction = { start: string; end: string } | null; // HH:mm (janela simples; dia-da-semana = evolução futura)
export type IpRestriction = { allowedPublicIps: string[] } | null;          // CIDR/IPv6 = evolução futura

// domain/models/user.ts
export type User = {
  id: number; login: string; name: string; email: string; active: boolean;
  // multiempresa (já existente — CLAUDE.md EspaçoN)
  role: UserRole; accessScope: AccessScope;
  franchiseId: number | null; representativeId: number | null;
  employeeId: number | null;          // vínculo opcional
  cashOperator: CashOperator;
  remote: boolean;
  accessTimeRestriction: AccessTimeRestriction;
  ipRestriction: IpRestriction;
  permissions: Permission[];          // FONTE DA VERDADE de acesso
  sourceProfileId: number | null;     // só modelo de cadastro — NUNCA autoriza
};

// domain/models/user-profile.ts
export type UserProfile = { id: number; description: string; permissions: Permission[] };

// shared/access — contrato de autorização
export type AccessRequest = {
  permissionKey: string; action: PermissionAction;
  remoteContext?: boolean; now?: Date; publicIp?: string;
};
export type AuthorizationResult = { allowed: true } | { allowed: false; reason: DenyReason };
```

`copyWith` e `fromJson/toJson` seguem o padrão do template.

---

## UseCases

`GetUsers`, `GetUserById`, `SaveUser`, `DeleteUser`,
`GetUserProfiles`, `GetUserProfileById`, `SaveUserProfile`, `DeleteUserProfile`,
`GetPermissionCatalog`, `ApplyProfileToUser`.
`AccessChecker` é função pura no kernel (não é UseCase).

## Repositories / Providers

* `UsersRepository`, `UserProfilesRepository`, `PermissionCatalogRepository`.
* Providers: `Mock*` agora; `Rest*` sob medida depois.

---

## Reuso e localização (classificação obrigatória)

| Item | Onde | Por quê |
| --- | --- | --- |
| `PermissionAction`, `Permission`, `AccessChecker`, `DenyReason` | **`shared/access`** | Transversal a todo o sistema |
| `useAccess()` + diretiva `v-can` | **`shared/access`** (glue presentation) | Usado por qualquer componente |
| Normalizador de descrição | **`shared/extensions`** | Consistência de match |
| `countByAction()` (contadores) | **`shared/access`** | Tela + relatórios |
| CRUD usuário/perfil + telas | **`modules/users`** | Específico do módulo |
| Widget **matriz de permissões** | `modules/users/presentation/widgets` | Reusado por usuário **e** perfil |
| **Catálogo de recursos** | mock próprio (`modules/users/data/mocks`) | Lista mestra; sidebar é consumidor |

---

## Ciclo de vida

* `AccessChecker`/normalizador: puros, sem instância.
* `PermissionCatalog`: cache/singleton (muda pouco).
* `UsersStore`/`UserProfileStore`: instância por tela (estado de edição).
* Repositories/Providers: injetados via factory.

---

## Telas (UX)

### Usuário

* Dados básicos + vínculo opcional com funcionário.
* Bloco **Caixa**: operador → tipo → `código` quando limitado.
* Switch **Remoto**. Blocos opcionais **Horário** e **IP**.
* **Seletor de perfil** → confirma → **redefine** todas as ações.
* **Matriz de permissões** (recursos agrupados por sessão × 9 ações):

```txt
                       abrir buscar novo modif. apagar imprim. relat. gráf. remoto
[Sessão: Comercial]     [▢]   [▢]    [▢]   [▢]    [▢]    [▢]     [▢]    [▢]   [▢]
   Cadastro de Cliente   ✔     ✔      ✔     ✔      ▢      ✔       ✔      ▢     ▢
   Desbloquear Cliente   —     —      ✔     —      —      —       —      —     ✔
   Contrato              ✔     ✔      ▢     ▢      ▢      ✔       ✔      ▢     ▢
[Sessão: Financeiro]    ...
─────────────────────────────────────────────────────────────────────────────
Contadores (ao vivo):    12    12     7     5      2      9       6      3     4
```

* Toggles **individuais**, **por sessão** e **por ação (coluna)**.
* **9 contadores ao vivo** (uma por ação), atualizados a cada alteração.
* Padrões de grid: filtro, ordenação, paginação, loading, vazio, erro.

### Perfil

Descrição + a mesma **matriz** (widget reutilizado).

---

## Mock Inicial

* `MockPermissionCatalogProvider`: recursos por sessão (itens de menu **e**
  não-menu, ex.: "Desbloquear Cliente", "Contrato").
* `MockUserProfilesProvider`: perfis ("Administrativo", "Caixa", "Atendimento").
* `MockUsersProvider`: cenários — operador limitado/ilimitado/não-operador,
  com/sem remoto, com/sem horário, com/sem IP, com/sem vínculo de funcionário.
* Tudo em `AsyncResult`.

---

## Integração Real (sob medida, por módulo, depois)

Backend de cada módulo será construído para casar com estes models. A camada
`data` concentra a conversão. Definir no backend: padronização da `descrição` e
formatos finais de horário/IP.

---

## Impactos

`domain/models`, `domain/enums`, `domain/repositories`, `data/application`,
`data/repositories`, `data/providers`, `data/mocks`, `presentation/*`,
**`shared/access` (novo)**, `shared/extensions`, `router` (guards),
`modules/home` (sidebar consome o checker) — **ADR-006**.

---

## Base de Conhecimento

Documentar: o que é usuário/perfil/permissão; que **perfil não dá acesso**; as 9
ações; operador de caixa (limitado×ilimitado); remoto; restrição de horário/IP;
uso da matriz (individual/sessão/ação/aplicar perfil); rota e permissões da tela.

---

## Pendências (não bloqueiam o mock; evolução futura)

* Formato rico de horário (múltiplas janelas / dia da semana).
* IP: faixas CIDR / IPv6.
* `supportedActions` por recurso no catálogo (refinar UI da matriz).

---

## Relação com outros documentos

* Detalha/embasa **ADR-006 (permissões)**; usa `AsyncResult`/`AppError` e
  `BaseCrudStore` (ADR-004). Provável **novo ADR**: "núcleo de autorização em
  `shared/access`".
