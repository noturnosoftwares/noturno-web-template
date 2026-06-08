# Design System — Noturno Softwares (Template Global)

> **Escopo e autoridade.** Este documento é a lei visual de **todo** projeto web da
> Noturno. Vale como nível template: qualquer produto (EspaçoN/HelpDesk, DoctorCar,
> Sales, Stock, Delivery, Portal, App Cliente…) herda estas regras e só pode
> *especializar* — nunca contrariar. Ele centraliza e substitui as orientações
> de UI espalhadas (`docs/ui`, paleta em `CLAUDE.md`): a paleta passa a viver aqui
> como camada de tokens.
>
> **Posição no repositório (recomendada):** `docs/design-system/README.md` no template.
>
> **Como a IA usa este doc.** Antes de criar qualquer tela, componente, campo, grid,
> diálogo ou card, a IA deve: (1) usar **apenas tokens** definidos aqui — nunca hex/px
> avulsos; (2) reutilizar os **componentes-base** desta especificação — nunca recriar
> um campo/botão/tabela do zero; (3) seguir os **padrões de tela**. Se algo não estiver
> coberto, a IA propõe um token/padrão novo *neste documento* antes de implementar.

---

## 0. Filosofia

A prioridade é a **experiência do usuário** sustentada por um visual **moderno, minimalista
e duradouro** — feito para envelhecer bem por décadas, não para impressionar numa demo.

1. **Dark-first.** O tema escuro é a base do projeto (não é a inversão de um tema claro).
   Profundidade no escuro nasce de **superfícies tonais + bordas sutis + brilhos suaves**,
   nunca de sombras pesadas.
2. **Minimalismo com intenção.** Cada elemento justifica sua presença. Sem poluição,
   sem enfeite vazio. Espaço em branco é ferramenta.
3. **Consistência por tokens.** Nada de valor "no olho". Toda cor, espaço, raio,
   tipografia e movimento sai de um token. Duas telas nunca renderizam o mesmo
   elemento de formas diferentes.
4. **Identidade Noturno.** A marca combina **preto, cinza e vermelho** (logo), com o
   **dourado** como assinatura. A interface respeita essa identidade e nunca usa cor
   fora da paleta.
5. **Movimento sutil sempre bem-vindo.** Efeitos suaves, nada extravagante. Cards e
   dashboards preferencialmente animados; estados de transição com vida, mas discretos.
6. **Reuso acima de tudo.** Antes de criar, verificar se já existe componente-base.
   O que serve a vários módulos vira `shared`; o que serve a vários produtos vira package.

---

## 1. Arquitetura de Tokens (3 camadas)

Padrão de mercado consolidado (Material 3, Carbon, Spectrum). Garante theming, baixa
manutenção e legibilidade por máquinas e pessoas.

```txt
Primitivo            Semântico                     Componente
(o que existe)       (qual o papel)                (onde exatamente)
#FFB621        →     accent / accent-hover    →    button-primary-bg
#040404        →     surface-canvas           →    page-bg
#FF2626        →     feedback-danger          →    dialog-delete-accent
```

* **Primitivo** — valores crus (a paleta oficial). Não se aplica direto na tela.
* **Semântico** — dá **significado** (superfície, texto, borda, accent, feedback).
  É o que a UI consome 95% do tempo. Trocar de tema = remapear semântico, sem renomear.
* **Componente** — só quando um componente precisa de ajuste fino próprio.

**Regra de ouro:** componentes consomem **semântico**, nunca primitivo direto.

---

## 2. Cor

### 2.1 Primitivos (paleta oficial — fonte da verdade)

| Token primitivo        | Hex       |
| ---------------------- | --------- |
| `orange`               | `#FFB621` |
| `orange-dark`          | `#FF9500` |
| `white`                | `#FFFFFF` |
| `grey-light`           | `#999999` |
| `grey-light-clean`     | `#D9D9D9` |
| `grey-900` (clean2)    | `#27272A` |
| `grey-800` (clean3)    | `#3F3F47` |
| `grey-dark`            | `#2E2F36` |
| `black`                | `#040404` |
| `black-2`              | `#1C1C1C` |
| `black-3`              | `#111820` |
| `black-secondary`      | `#131417` |
| `red`                  | `#FF2626` |
| `yellow`               | `#FFDA0C` |
| `green`                | `#00BA81` |
| `green-light`          | `#96D544` |
| `blue`                 | `#036AE3` |
| `blue-dark`            | `#3FA5EE` |
| `blue-light`           | `#21A5EE` |

> Recomendação de implementação: manter os primitivos em hex (fonte da verdade) e gerar
> *estados* (hover, tints, alpha) preferencialmente em **OKLCH** no Tailwind v4 — é
> perceptualmente uniforme e torna previsível clarear/escurecer e criar tints sem o
> accent "estourar" no escuro.

### 2.2 Semânticos

**Superfícies (a escada dark-first — do fundo ao topo).** Quanto mais "alto" o
elemento, mais clara a superfície.

| Token semântico        | Primitivo            | Uso                                            |
| ---------------------- | -------------------- | ---------------------------------------------- |
| `surface-canvas`       | `black` / `black-secondary` | Fundo da aplicação (atrás de tudo)      |
| `surface-1`            | `black-2`            | Cards, painéis, tabela                         |
| `surface-2`            | `grey-900`           | Card elevado, header de tabela, hover de linha |
| `surface-3`            | `grey-800`           | Overlay: modal, dropdown, popover              |

**Texto.**

| Token                  | Primitivo            | Uso                                            |
| ---------------------- | -------------------- | ---------------------------------------------- |
| `text-primary`         | `white`              | Títulos, conteúdo principal                    |
| `text-secondary`       | `grey-light-clean`   | Texto de apoio, labels destacados              |
| `text-muted`           | `grey-light`         | Hints, placeholders, metadados                 |
| `text-on-accent`       | `black`              | Texto sobre botão dourado                      |

**Bordas.**

| Token                  | Primitivo            | Uso                                            |
| ---------------------- | -------------------- | ---------------------------------------------- |
| `border-subtle`        | `grey-800` @ ~40%    | Divisória de linha, separação leve             |
| `border-default`       | `grey-800`           | Borda de campo, card                            |
| `border-strong`        | `grey-dark`→claro    | Hover de campo                                  |
| `border-focus`         | `accent`             | Foco de campo/botão (anel dourado)             |

**Marca / accent.**

| Token                  | Primitivo            | Uso                                            |
| ---------------------- | -------------------- | ---------------------------------------------- |
| `accent`               | `orange`             | Ação principal, item ativo, destaque de marca  |
| `accent-hover`         | `orange-dark`        | Hover do accent                                |
| `accent-soft`          | `orange` @ 14%       | Tint de fundo para destaque/ativo no menu      |

**Feedback (estados semânticos).** Sempre **cor + ícone + rótulo**.

| Token                  | Primitivo            | Tint de fundo  | Uso                          |
| ---------------------- | -------------------- | -------------- | ---------------------------- |
| `feedback-success`     | `green`              | `green` @ 14%  | Sucesso, ativo               |
| `feedback-danger`      | `red`                | `red` @ 14%    | Erro, exclusão, perigo, bloqueio |
| `feedback-warning`     | `yellow`             | `yellow` @ 14% | Atenção, pendência           |
| `feedback-info`        | `blue-light`         | `blue` @ 14%   | Informação, neutro positivo  |

### 2.3 Regras de cor dark-first (obrigatórias)

* **Nunca** usar sombra como **único** recurso de profundidade. Separe por superfície
  tonal + `border-subtle`. Sombra só em camadas flutuantes (modal/menu) e suave.
* **Accents em tinta cheia só em elementos pequenos** (ícone, ponto, texto, botão).
  Para fundos de estado (badge, alerta, linha selecionada) use o **tint** (@12–16%).
* **Vermelho** é cor de marca (logo) e pode pesar — mas no escuro é saturado: prefira
  tint para áreas grandes e tinta cheia só em ação destrutiva/ícone/borda.
* **Status (situação):** mapeamento recomendado — *Ativo* → success; *Inativo* →
  `text-muted`/neutro (não é erro); *Bloqueado/Cancelado* → danger; *Pendente* → warning.
* **Contraste:** texto ≥ 4.5:1; texto grande ≥ 3:1. (Botão dourado usa `text-on-accent`
  preto — contraste alto, ok.)

---

## 3. Tipografia

Fonte: **Inter** (fallback `system-ui, -apple-system, Segoe UI, Roboto, sans-serif`).
Ícones: **PrimeIcons**.

| Token            | Tamanho | Peso | Linha | Uso                                       |
| ---------------- | ------- | ---- | ----- | ----------------------------------------- |
| `text-display`   | 30px    | 700  | 1.2   | Hero de página ("Bom dia, …")             |
| `text-h1`        | 24px    | 700  | 1.25  | Título de página ("Usuários")             |
| `text-h2`        | 20px    | 600  | 1.3   | Título de seção/card                      |
| `text-h3`        | 16px    | 600  | 1.4   | Subtítulo                                 |
| `text-body`      | 14px    | 400  | 1.5   | Texto padrão da UI                        |
| `text-body-strong` | 14px  | 600  | 1.5   | Ênfase em corpo                           |
| `text-label`     | 13px    | 500  | 1.4   | Rótulo de campo                           |
| `text-hint`      | 12px    | 400  | 1.4   | Hint, helper, metadado                    |
| `text-eyebrow`   | 11px    | 600  | 1.3   | Sobrescrito em CAPS ("NOTURNO SOFTWARES") |

---

## 4. Espaçamento (base 4px)

| Token       | px  |     | Token        | px  |
| ----------- | --- | --- | ------------ | --- |
| `space-1`   | 4   |     | `space-6`    | 24  |
| `space-2`   | 8   |     | `space-8`    | 32  |
| `space-3`   | 12  |     | `space-10`   | 40  |
| `space-4`   | 16  |     | `space-12`   | 48  |
| `space-5`   | 20  |     | `space-16`   | 64  |

Atalhos semânticos: **padding de página** `space-6`/`space-8`; **padding interno de card**
`space-5`/`space-6`; **gap entre campos** `space-4`; **gap label↔campo** `space-2`.

---

## 5. Raio de Borda

| Token          | Valor   | Uso                                   |
| -------------- | ------- | ------------------------------------- |
| `radius-sm`    | 6px     | Chips pequenos, tags                  |
| `radius-md`    | 10px    | Campos, botões                        |
| `radius-lg`    | 14px    | Cards, tabela                         |
| `radius-xl`    | 20px    | Painéis grandes, modais               |
| `radius-full`  | 9999px  | Avatar, badge pílula, botão-ícone     |

---

## 6. Elevação & Profundidade (dark)

Sem drop-shadow como recurso primário. Profundidade = **degrau de superfície + borda**;
sombra suave **só** para camadas flutuantes.

| Nível    | Receita                                                              | Uso                          |
| -------- | ------------------------------------------------------------------- | ---------------------------- |
| `elev-0` | `surface-canvas`, sem borda                                         | Região de fundo              |
| `elev-1` | `surface-1` + `border-subtle` 1px                                   | Card padrão, tabela          |
| `elev-2` | `surface-2` + `border-subtle` 1px (+ glow muito leve no hover)      | Card elevado, hover          |
| `elev-3` | `surface-3` + `border-default` + scrim `rgba(0,0,0,.5)` + sombra suave | Modal, dropdown, popover  |

---

## 7. Movimento

Efeitos suaves, nunca extravagantes. Respeitar sempre `prefers-reduced-motion`.

| Token              | Valor                          | Uso                                 |
| ------------------ | ------------------------------ | ----------------------------------- |
| `duration-fast`    | 120ms                          | Hover, micro-estado                 |
| `duration-base`    | 200ms                          | Transições gerais, lift de card     |
| `duration-slow`    | 320ms                          | Entrada de modal/drawer, reveal     |
| `easing-standard`  | `cubic-bezier(0.2,0,0,1)`      | Padrão                              |
| `easing-enter`     | `cubic-bezier(0,0,0,1)`        | Entrada (desacelera)                |
| `easing-exit`      | `cubic-bezier(0.3,0,1,1)`      | Saída (acelera)                     |

**Padrões de animação aprovados:** lift sutil de card no hover (translateY -2px +
glow leve); entrada de listas/cards em *stagger* curto; contagem animada de números em
KPI; *pulse* gentil (1.6–2s) no ícone de empty-state; slide+fade de toasts e modais.
**Proibido:** bounce exagerado, parallax pesado, animações longas que travam o fluxo.

---

## 8. Componentes-Base Obrigatórios

> Esta é a camada que faltava e causou a quebra. **Nenhuma tela monta campo, botão,
> tabela, diálogo, toast ou estado vazio "na mão".** Tudo vem daqui (em `shared/widgets`).

### 8.1 Campo (`BaseField` e derivados)

Anatomia: **label (em cima)** → **controle** → **hint/erro (embaixo)**.

| Estado     | Borda             | Texto/Extra                                   |
| ---------- | ----------------- | --------------------------------------------- |
| Padrão     | `border-default`  | label `text-secondary`/`text-primary`; placeholder `text-muted` |
| Hover      | `border-strong`   | —                                             |
| Foco       | `border-focus` (dourado) + anel suave | —                         |
| Erro       | `feedback-danger` | helper em `feedback-danger` + ícone de erro   |
| Desabilitado | `border-subtle` | opacidade .4, sem cursor                       |

* Fundo: `surface-1`; raio `radius-md`; altura mínima 40px.
* **Ícone dentro do campo nunca sobrepõe o texto.** Se houver ícone à esquerda (ex.: busca),
  o input recebe `padding-left = largura do ícone + space-3`. *(Bug visto na tela de Usuários.)*
* Derivados padronizados em `shared/widgets`: `SearchField`, `MoneyField`, `DateField`,
  `CpfField`, `CnpjField`, `CepField`, `PhoneField`. Formatação/validação vêm de
  `shared/extensions` — nunca recodificar máscara por tela.

### 8.2 Botão (`BaseButton`)

| Variante   | Aparência                                              | Quando usar                              |
| ---------- | ------------------------------------------------------ | ---------------------------------------- |
| `primary`  | fundo `accent`, texto `text-on-accent`, hover `accent-hover` | Ação que **inicia algo** (Novo X, Salvar, Entrar) |
| `danger`   | tinta cheia `feedback-danger` (texto branco) ou tint+borda no secundário | Ação destrutiva                |
| `neutral`  | transparente/`surface-1` + `border-default`, texto `text-secondary`, hover `surface-2` | Secundário, Cancelar |
| `icon`     | quadrado ~40px, `radius-md`, estilo ghost              | Toolbar, header de diálogo               |

* **Ícone sempre que ajudar** a entender a ação. Altura mínima 40px (toque).
* Desabilitado: opacidade .4, sem ponteiro. Loading: spinner + texto, sem trocar largura.

### 8.3 Badge de status (`StatusBadge`)

Fundo = tint do feedback (@14%); texto/ponto/ícone na cor cheia do feedback.
**Sempre cor + ícone + rótulo.** Usar o mapeamento de status da seção 2.3.

### 8.4 Card (`BaseCard`)

`elev-1` por padrão; `radius-lg`; padding `space-5/6`; título `text-h2` com ícone opcional.
Hover de card interativo: lift sutil (seção 7). Dashboards usam cards **animados**.

### 8.5 Tabela / Grid (`BaseDataTable`)

* Sempre **dentro de um card** (`elev-1`, `radius-lg`).
* Header em `surface-2`, texto `text-body-strong`/`text-secondary`, com indicador de
  ordenação clicável.
* Linhas com **divisão clara**: `border-subtle` entre linhas **ou** zebra (tint alternado).
  Hover de linha realça (`surface-2`). Altura confortável (~52–56px).
* **Nunca colocar botões de ação inline no grid de pesquisa.** O grid de busca é para
  **ler/escanear**. Ações se dão por: clique na linha → detalhe/edição; seleção → toolbar
  contextual; ou (exceção) um único menu "mais" por linha quando indispensável.
* Estados: **loading** (skeleton de linhas), **vazio** (empty-state da seção 9.1), **erro**.

### 8.6 Paginação (`BasePagination`)

Sempre presente em resultados de pesquisa. Boa apresentação: primeiro/anterior/página/
próximo/último + faixa ("1–20 de 134"). Seletor de tamanho de página opcional.
Centralizada ou à direita, com respiro.

### 8.7 Diálogo (`BaseDialog`)

Overlay com scrim + painel `elev-3`, `radius-xl`. Header (título + **cor da finalidade**),
corpo, rodapé com ações. A **cor representa a finalidade**:

| Finalidade        | Accent do diálogo | Botão de confirmação        |
| ----------------- | ----------------- | --------------------------- |
| Exclusão/perigo   | `feedback-danger` | `danger`                    |
| Confirmação geral | `accent`          | `primary`                   |
| Sucesso/positivo  | `feedback-success`| `primary`                   |
| Informação        | `feedback-info`   | `neutral`/`primary`         |

### 8.8 Toast (`BaseToast`)

* **Posição: canto superior esquerdo** ("vindo de cima da esquerda"), com slide+fade.
* Tipos success/info/warning/danger (cor + ícone + mensagem). Auto-dismiss ~4s,
  dispensável, empilhável.
* **Disparo pós-ação:** ex. após excluir → "Usuário excluído."; após salvar → "Salvo."

### 8.9 Shell de aplicação (`AppShell`)

Toda tela autenticada vive dentro do shell: **sidebar** (recolhível) + **topbar**
(busca Ctrl+K, notificações, ajuda, perfil) + área de conteúdo em `surface-canvas`.
**Proibido** página solta full-bleed dentro do app. *(Quebra vista na tela de Usuários.)*

---

## 9. Padrões de Tela

### 9.1 Página de pesquisa

* **Abre vazia** — nunca carrega todos os dados ao entrar. No lugar do grid, mostra uma
  **arte de empty-state** convidando à busca: ícone que **remete ao domínio** (usuários →
  `pi-users`; clientes → `pi-building`; contratos → `pi-file`; chamados → `pi-headphones`)
  com **pulse gentil**, e copy amigável **não repetitiva**. Banco de variações sugerido:
  "Pronto para pesquisar.", "Comece pela busca acima.", "Busque para ver os resultados.",
  "Digite e pressione Enter."
* **Busca por Enter ou botão** — **nunca** busca incremental no backend a cada tecla.
  Se os dados já estiverem em **cache**, a digitação filtra no cliente.
* **Filtros sempre que possível**; **ordenação**; **paginação sempre**.
* Quando fizer sentido, **cards de situação** no topo com dados importantes do resultado
  (totais, críticos, etc.).
* Sem ações inline no grid (ver 8.5).

### 9.2 Formulários e CRUD

* **Agrupar campos por contexto** em seções/cards com título.
* **Não poluir:** no máximo 2 (às vezes 3) campos por linha no desktop; analisar a
  disposição **antes** de implementar; campos longos ocupam a linha inteira.
* **Combobox/Lookup só para dados estáticos** (listas pequenas e fixas). Dado vindo do
  **backend** usa **campo de busca/lookup** (type-to-search), nunca um select gigante.
  Filtros de listagem ficam em **search fields**.
* **Confirmar antes** de: cancelar, pagar, excluir e qualquer ação destrutiva/irreversível.
* **Guarda de navegação:** sair de uma tela em modo **inclusão/edição** com alterações não
  salvas → confirmar a substituição/descarte da tela antes de navegar.

### 9.3 Dashboard

Layout **bento** (CSS Grid/subgrid): cards maiores guiam a leitura, menores dão apoio.
Cards **animados** (entrada em stagger, hover lift, números com contagem). Chrome do card
sempre consistente; tint de ícone do KPI **pode** ser semântico por métrica, mas a
superfície/borda do card é igual em todos.

---

## 10. Padrões de Plataforma de Gestão (ERP)

> Telas de gestão são usadas por **especialistas sob pressão**, o dia inteiro. O mercado
> (Salesforce Lightning, Microsoft Fluent/Dynamics, Oracle APEX) padronizou em torno de
> **eficiência, densidade de informação e operação por teclado** — não de enfeite visual.
> Os padrões abaixo são obrigatórios em telas de gestão (cadastros, lançamentos, documentos).

### 10.1 Operação keyboard-first

* Atalhos globais (paleta de comandos **Ctrl+K** no shell). **Enter** confirma/dispara busca;
  **Esc** fecha diálogo/drawer; **Tab** em ordem lógica (campo → campo → ação primária).
* "Novo registro" e "Salvar" acessíveis por atalho. Ao abrir um form, **foco automático**
  no primeiro campo útil.
* Atalhos **descobríveis**: tooltip mostra a tecla; a tela de Ajuda lista os atalhos.

### 10.2 Densidade configurável

* Dois modos: **confortável** (padrão) e **compacto**, **persistidos por usuário**.
* Afeta altura de linha de grid e padding de campo/card (compacto reduz row height ~52→40px
  e o padding interno). Power user quer compacto; gestor casual quer respiro.

### 10.3 Localização e formatação (pt-BR)

* **Moeda** `R$ 1.234,56`; **data** `dd/MM/aaaa`; **número** com milhar `.` e decimal `,`.
* Centralizado em helpers de `shared/extensions` (`formatCurrency`, `formatDate`,
  `formatNumber`) e consumido pelos fields (`MoneyField`, `DateField`). **Nunca** formatar
  "na mão" por tela. Entrada respeita o mesmo locale (máscara de moeda/data).

### 10.4 Grid de dados editável (`BaseDataGrid`)

Distinto do **grid de pesquisa** (8.5, que é só leitura). Para entrada/edição em massa:

* **Edição inline** por célula, com navegação por teclado (Tab/Enter/setas), validação por
  célula e linha de novo registro.
* Recursos de grid pesado: **colunas fixas/congeladas** (primeira coluna identificadora),
  **agrupamento**, **ordenação multi-coluna**, **redimensionar/reordenar/ocultar colunas**
  (persistido por usuário) e **linha de totais/somatórios** — **obrigatória** em grids
  financeiros.
* Estados loading/vazio/erro como em 8.5.

### 10.5 Master-detail com side drawer

* O grid mostra **5–7 colunas críticas**; selecionar a linha abre um **drawer lateral**
  (`elev-3`) com o registro completo, **preservando o contexto** da lista (sem perder o lugar).
* Para registros muito densos, o drawer dá lugar à **página de detalhe** (10.6).

### 10.6 Página de detalhe (header + abas)

* **Header da entidade:** identificação + `StatusBadge` + ações primárias (contextuais por
  estado, 10.7).
* **Conteúdo em abas:** Dados, Financeiro, Histórico, Anexos (conforme o módulo).
* **Campos de auditoria** (criado/alterado por e quando) discretos, na aba Histórico.

### 10.7 Ações em lote e contextuais por estado

* **Multi-seleção** no grid → **toolbar contextual** de ações em lote.
* **Mostrar só as ações válidas para o estado** do registro (ex.: "Aprovar"/"Rejeitar"
  apenas em *pendente*). Não exibir todas as ações o tempo todo — reduz carga visual e
  cliques destrutivos acidentais. Ação destrutiva sempre confirma (8.7).

### 10.8 Filtros e visões salvas

* Filtros sempre que possível; **chips de filtro rápido** acima do grid mostrando o que
  está aplicado (removíveis).
* **Visões salvas** por usuário (combinação de filtros + colunas + ordenação), nomeadas.

### 10.9 Workflow de status (máquina de estados)

* Documentos têm **ciclo de vida explícito** (ex.: rascunho → enviado → aprovado → cancelado).
* Estado atual **sempre visível** via `StatusBadge` (mapa de cor da 2.3); transições
  permitidas expostas como ações contextuais (10.7); transição irreversível confirma.

### 10.10 Barra de ação fixa em formulários longos

* `Salvar`/`Cancelar` em **barra fixa** (sticky) sempre acessível, independente do rolar.
* **Resumo de validação** no topo ao submeter com erros; **campos obrigatórios marcados**.
* Combina com a guarda de navegação de 9.2 (descartar não-salvo).

---

## 11. Acessibilidade & Usabilidade

* Contraste de texto ≥ 4.5:1 (grande ≥ 3:1); texto de botão ≥ 4.5:1.
* Status sempre **cor + ícone + rótulo** (nunca só cor).
* Foco de teclado **visível** (anel dourado), ordem de tab coerente, **Enter** dispara a busca.
* Toda tela prevê os estados **loading / vazio / erro / sucesso**.
* Responsivo: desktop, notebook, tablet e telas menores quando possível.

---

## 12. Anti-padrões (proibido)

Lista direta — boa parte saiu da tela de Usuários:

* Página sem `AppShell` (solta no preto, sem sidebar/topbar).
* Campo sem container/borda, ou com **ícone sobreposto** ao texto.
* "Botão" como texto+ícone sem chrome de botão.
* Tabela sem card/superfície ou sem divisão de linhas.
* **Botões de ação inline no grid de pesquisa.**
* Página de pesquisa que **carrega dados ao abrir**.
* **Busca incremental no backend** a cada tecla.
* Status comunicado **só por cor**.
* Sombra como único recurso de profundidade no escuro.
* Cor/medida fora dos tokens (hex/px avulso).
* Duas telas renderizando o mesmo elemento de formas diferentes.
* **Todas as ações sempre visíveis**, ignorando o estado do registro.
* **Grid financeiro sem linha de totais.**
* Data/moeda/número **fora do pt-BR** ou formatado "na mão" na tela.
* Form longo **sem barra de ação fixa** (Salvar/Cancelar perdidos no rolar).
* Edição em massa **sem navegação por teclado**.

---

## 13. Implementação na stack (resumo)

* **Tokens** em `src/app/globals.css` via Tailwind v4 `@theme`:
  primitivos `--color-noturno-*` + semânticos (`--color-surface-1`, `--color-text-muted`,
  `--color-border-focus`, `--radius-card`, `--space-*`, `--duration-*`…). Tema escuro
  ativado por `.dark` no `<html>`.
* **PrimeVue (preset Aura)** mapeado para os tokens semânticos — não estilizar componente
  PrimeVue por fora com cor crua; ajustar via tokens.
* **Componentes-base** desta spec em `src/shared/widgets` (incl. `BaseDataGrid`, side drawer,
  toolbar de lote, barra de ação fixa, chips de filtro); formatadores/validadores e os
  helpers de localização pt-BR (`formatCurrency`/`formatDate`/`formatNumber`) em
  `src/shared/extensions`.
* Preferência de **densidade** e **visões/colunas salvas** persistidas por usuário
  (via `KeyValueStore`).
* Ordem de camadas CSS: `tailwind-base, primevue, tailwind-utilities`.

---

## 14. Checklist de conformidade (tela "pronta")

* [ ] Dentro do `AppShell` (sidebar + topbar).
* [ ] Usa componentes-base; zero campo/botão/tabela "na mão".
* [ ] Apenas tokens (sem hex/px avulso, sem cor fora da paleta).
* [ ] Pesquisa: abre vazia, empty-state com ícone contextual + pulse, busca por Enter/botão.
* [ ] Paginação presente; sem ações inline no grid de pesquisa; ordenação onde cabe.
* [ ] Diálogos coloridos por finalidade + confirmações (cancelar/pagar/excluir) + guarda de navegação.
* [ ] Toasts no topo-esquerdo após ações.
* [ ] Forms agrupados por contexto e sem poluição; combobox/lookup conforme regra.
* [ ] Estados loading/vazio/erro/sucesso previstos.
* [ ] Contraste e foco de teclado ok; responsivo.
* [ ] Animações sutis aplicadas; nada extravagante.
* [ ] Operável por teclado (Enter/Esc/Tab; atalhos onde fizer sentido) e densidade respeitando o usuário.
* [ ] Datas/moeda/números em pt-BR via helpers (sem formatação manual).
* [ ] Detalhe denso via master-detail/drawer ou página com header + abas; auditoria no Histórico.
* [ ] Ações contextuais por estado; lote via seleção; grid financeiro com totais; colunas/visões persistidas.
* [ ] Form longo com barra de ação fixa + resumo de validação + obrigatórios marcados.
* [ ] Documentos com status visível e transições explícitas.
