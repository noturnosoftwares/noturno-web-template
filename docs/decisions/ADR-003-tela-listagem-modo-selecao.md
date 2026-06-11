# ADR-003 — Tela de listagem como consulta reutilizável (modo gestão × modo seleção)

## Status

Aceito

## Data

2026-06-11

## Contexto

Cadastros frequentemente precisam referenciar outros cadastros. Exemplo concreto:
o cadastro de usuário precisa escolher um **perfil**, e o perfil já possui tela de
listagem/cadastro implementada.

Na implementação atual foi criada uma **segunda tela**, separada, apenas para
pesquisar e selecionar o perfil. Isso:

- duplica uma tela de listagem que já existe;
- fere a regra corporativa de reuso (ver `../template/CLAUDE.md`,
  "Reaproveitamento de Componentes e Utilitários");
- cria divergência de comportamento (duas telas para o mesmo dado tendem a evoluir
  de formas diferentes);
- aumenta custo de manutenção sem ganho.

Precisamos de um padrão único: a própria tela de cadastro/listagem de um dado serve
também como tela de **consulta e retorno** (padrão de *lookup* clássico de cadastro).
Como o padrão altera o comportamento de gestos (Enter, duplo clique, clique),
introduz um contrato de retorno e afeta **todos os módulos**, a decisão é
arquitetural e precisa de ADR.

## Opções avaliadas

### Opção 1 — Rota + store de seleção

A tela de listagem continua sendo uma **página**. É aberta pela tela solicitante
com um parâmetro de rota (`mode=select`) e devolve o registro selecionado através
de uma **store compartilhada de seleção**.

- Benefícios: mantém o padrão "tudo é página" do projeto; permite deep-link e botão
  voltar nativo; reaproveita exatamente a mesma tela sem embuti-la em outro lugar;
  uma única fonte de verdade por dado; integra-se ao fluxo Page → Store →
  Application já adotado.
- Riscos: exige uma store compartilhada como canal de retorno; é necessário
  cuidado para limpar requisições de seleção pendentes (evitar vazamento de estado
  entre fluxos).

### Opção 2 — Diálogo/modal que resolve uma Promise

A listagem é aberta dentro de um diálogo (modal) que resolve uma `Promise` com o
registro selecionado (API `await`).

- Benefícios: API de chamada simples (`const perfil = await selecionarPerfil()`);
  não precisa de store para o retorno.
- Riscos: a `Promise` não sobrevive a navegação/recarregamento; foge do padrão
  "tudo é página"; dificulta deep-link; exige a listagem embutida em modal, o que
  conflita com telas de listagem que são páginas completas.

### Opção escolhida — Opção 1 (Rota + store de seleção)

Escolhida por preservar o padrão de páginas do projeto, permitir o reaproveitamento
direto da tela já existente (sem precisar embuti-la em modal) e por se encaixar no
fluxo Page → Store → Application e na hierarquia de stores (`BaseCrudStore`) já
estabelecidos no template.

## Decisão

### O que será feito

Toda tela de listagem/cadastro passa a operar em **dois modos**, controlados por
parâmetro de rota, e a servir como única fonte de pesquisa e seleção do seu dado.

### Padrão adotado

1. **Reuso obrigatório.** Antes de criar qualquer busca/seleção de um dado, verificar
   se já existe a tela de listagem daquele dado. Se existir, ela é reutilizada em
   modo seleção. É **proibido** criar uma segunda tela só para pesquisar/selecionar
   um dado que já possui listagem.

2. **Dois modos, uma tela só.** O modo é um parâmetro de entrada da tela
   (`mode=select`), nunca uma tela nova:
   - **modo gestão** (padrão / standalone): navegação e CRUD completos;
   - **modo seleção**: aberta por outra tela para devolver um registro.

3. **Comportamento dos gestos por modo:**
   - modo gestão → Enter / duplo clique / clique abrem **detalhes**;
   - modo seleção → Enter / duplo clique / clique **confirmam e devolvem** o
     registro à tela solicitante (não abrem detalhes).

4. **Botão "Ver detalhes" — apenas no modo seleção.** Como em modo seleção os
   gestos (clique/Enter/duplo clique) ficam reservados para **devolver** o registro,
   é preciso um caminho explícito para abrir o registro: por isso a coluna/botão
   **Ver detalhes** existe **somente nesse modo**. Em **modo gestão** ela é
   **omitida** — o clique na linha já abre os detalhes, então o botão seria
   redundante. O botão **Incluir** aparece nos dois modos.

5. **Reentrada de modo nos detours.** Abrir **Ver detalhes** ou **Incluir** durante
   o modo seleção é um *detour*: a query `mode/req` é **levada adiante** para a tela
   de detalhe/inclusão e **devolvida** quando ela retorna à listagem, de modo que a
   listagem **reentra em modo seleção** e o registro editado/incluído fica
   imediatamente selecionável. A seleção **não** é encerrada por um detour — só por
   confirmar ou cancelar.

6. **Canal de retorno (rota + store).** Uma store compartilhada de seleção atua como
   canal de retorno entre a tela solicitante e a listagem.

### Convenção de rota

A tela solicitante registra uma requisição na store, recebe um `id` e navega para a
listagem com a query `?mode=select&req=<id>`. A listagem, em modo seleção, resolve a
requisição na store e **navega para o `returnTo`** da requisição — **não**
`router.back()`. Motivo: a listagem pode ter *detours* no histórico (Ver detalhes,
Incluir), e o `back()` cairia no detalhe e não na solicitante; o `returnTo` é
robusto a qualquer profundidade de histórico. A tela solicitante consome o resultado
ao ser reativada.

### Contratos (em `shared/selection/`)

```ts
// shared/selection/selection-types.ts
export type SelectionRequest = {
  id: string;          // id único da requisição
  resource: string;    // recurso pedido, ex.: 'perfis'
  returnTo: string;    // rota da tela solicitante
  multiple?: boolean;  // seleção múltipla (reservado p/ futuro)
};

export type SelectionResult<T = unknown> =
  | { status: 'selected'; data: T }
  | { status: 'cancelled' };
```

A `SelectionStore` (Pinia, em `shared/selection/` ou `shared/stores/`) mantém um mapa
`requestId → pendente | resultado`, expondo, por contexto:

```txt
Commands
  open(req): id           // registra requisição, retorna o id (antes de navegar)
  resolve(id, data)       // listagem confirma a seleção
  cancel(id)              // listagem cancela
Queries
  consume(id): SelectionResult | null   // solicitante lê e limpa o resultado
```

### Limites a respeitar

- A seleção em si é entrega de UI de um registro já carregado pela listagem; ela
  **não** cria novo UseCase/Repository só para "devolver". O carregamento dos dados
  da listagem continua passando por Store → Application → Repository → Provider.
- Nenhuma chamada de API na page; retornos via `AsyncResult`.
- A store de seleção não guarda regra de negócio — é apenas canal de handoff.

### O que não deve ser feito

- Não criar segunda tela de pesquisa para um dado que já tem listagem.
- Não usar diálogo/modal com `Promise` como mecanismo de retorno (ver Opção 2).
- Não alterar o comportamento da listagem em modo gestão.
- Não reservar Enter/duplo clique para "devolver" em modo gestão.
- Não mostrar a coluna/botão "Ver detalhes" em modo gestão (redundante com o clique
  na linha) — ela é exclusiva do modo seleção.
- Não encerrar a seleção ao abrir um detour (Ver detalhes/Incluir): a query
  `mode/req` deve ser levada e devolvida para a listagem reentrar em seleção.
- Não usar `router.back()` para devolver o registro — usar o `returnTo` (robusto a
  detours).

## Consequências

### Benefícios

- Elimina telas duplicadas de pesquisa; uma única fonte de verdade por dado.
- Reduz acoplamento e custo de manutenção (uma tela evolui, todos os fluxos ganham).
- Padroniza gestos e botões entre todos os módulos.
- Preserva o fluxo contínuo (Ver detalhes abre o registro mesmo em modo seleção).
- Mantém o padrão de páginas, deep-link e navegação nativos.

### Riscos

- Requer store compartilhada de seleção e disciplina para limpar requisições
  pendentes (evitar vazamento de estado entre fluxos).
- Telas de listagem existentes precisam ser adaptadas para reconhecer `mode=select`.
- Exige cuidado com permissões: abrir uma listagem em modo seleção deve respeitar a
  permissão do usuário sobre aquele dado (ver ADR de permissões do produto).

### Impactos

- `shared/selection` (novo): tipos e `SelectionStore`;
- `shared/stores`: relação com `BaseCrudStore` (suporte a modo seleção);
- `router`: convenção de query `mode=select` / `req`;
- `presentation/pages` e `presentation/widgets` de todas as listagens;
- documentação: `docs/architecture`, `docs/specifications`, `docs/modules` e Base de
  Conhecimento dos módulos afetados.

## Quando revisar esta decisão

- Quando a API real existir e mudar a forma de carregar/paginar as listagens;
- Se o modelo de navegação do app mudar (ex.: adoção de modais como padrão);
- Se surgir necessidade de seleção múltipla em larga escala que o contrato atual não
  atenda bem;
- Se a store de seleção começar a acumular responsabilidade indevida.

## Relação com outros ADRs

- Complementa as regras corporativas de reuso de componentes/telas do template.
- Refina a hierarquia de stores (`BaseStore` → `BaseCrudStore` → store específica),
  acrescentando suporte a modo seleção sem revogá-la.
- Deve respeitar o ADR de permissões/perfis de acesso ao abrir listagens em seleção.

## Observações

- Padrão equivalente ao *lookup* clássico de cadastro (consulta "F3"): o cadastro é a
  própria tela de pesquisa e retorno.
- Caso da primeira aplicação: cadastro de usuário selecionando perfil — a tela de
  perfil existente passa a ser aberta em modo seleção em vez de uma tela nova.
  Implementado no EspaçoN (`shared/selection`, `/perfis?mode=select`).