# ADR-001 — Cancelar edição restaura o registro e permanece no detalhe

## Status

Aceito

## Data

2026-06-10

## Contexto

O padrão de CRUD do template define a hierarquia `BaseStore → BaseCrudStore →
Store específica`. Até aqui, o "Cancelar" das telas de cadastro/edição apenas
**navegava de volta para a listagem/pesquisa**, sem distinguir se o usuário
estava **incluindo** um registro novo ou **editando** um existente.

Isso gerava dois problemas de experiência em telas de gestão (usadas o dia
inteiro por especialistas):

- ao cancelar uma **edição**, o usuário perdia o lugar (voltava à pesquisa) mesmo
  querendo apenas **desfazer** as alterações e continuar olhando o registro;
- não havia restauração explícita do estado anterior — a "volta" dependia de
  recarregar a tela, e qualquer estado intermediário de edição ficava ambíguo.

Como o comportamento de cancelar é **transversal a todo CRUD** da Noturno, a
decisão pertence à camada corporativa (template) e deve ser implementada na
`BaseCrudStore` para valer em todos os módulos e produtos.

## Opções avaliadas

### Opção 1 — Manter "cancelar = voltar para a listagem" sempre

Simples, mas trata inclusão e edição igual. Perde o contexto do registro em
edição e não há restauração explícita do estado anterior. Mantém o problema.

### Opção 2 — Snapshot por tela (cada page guarda o original)

Cada formulário guardaria seu próprio `original` e restauraria na mão. Funciona,
mas **duplica** a lógica em cada módulo, contraria a regra de não repetir estado
comum entre stores e diverge facilmente entre telas.

### Opção escolhida — Snapshot imutável na `BaseCrudStore`

A `BaseCrudStore` passa a manter o estado de edição com **snapshot imutável** do
registro ao entrar em edição (`beginEdit`) e expõe `cancelEditing()`, que:

- em **edição** (havia registro anterior) → **restaura** o snapshot original em
  `editing` e devolve `'stay'`, sinalizando que a tela deve **permanecer no
  detalhe**;
- em **inclusão** (registro novo, sem snapshot) → **descarta** a edição e devolve
  `'leave'`, sinalizando que a tela deve **voltar para a listagem/pesquisa**.

A imutabilidade dos models (copyWith) garante que o snapshot nunca é mutado: cada
alteração cria nova instância via `setEditing`, deixando o snapshot intacto para
a restauração.

## Decisão

- A `BaseCrudStore` é a **fonte única** do estado de edição com snapshot:
  `beginCreate(blank)`, `beginEdit(record)`, `setEditing(record)`,
  `commitEditing(saved)`, `clearEditing()` e `cancelEditing(): 'stay' | 'leave'`.
- **Confirmar antes de cancelar quando há alterações não salvas.** Cancelar é uma
  ação que **descarta alterações**; por isso, com o registro *dirty*, a tela
  **pergunta antes** ("Cancelar alterações?" — diálogo de finalidade `danger`,
  com "Continuar editando" como saída). Sem alterações, cancela direto (não há o
  que perder). Só **após confirmar** é que se aplica a restauração/navegação
  abaixo. Isso vale para inclusão e edição (qualquer estado *dirty*).
- **Cancelar uma edição** restaura o registro original (snapshot) e **mantém o
  usuário na tela de detalhe**. Não navega para a pesquisa.
- **Cancelar um registro novo** (sem objeto anterior) descarta a inclusão e
  **volta para a listagem/pesquisa**.
- A página (presentation) **não** decide a regra: pergunta a confirmação quando
  `isDirty`, chama `cancelEditing()` e apenas **reage** ao resultado (`stay` →
  permanece; `leave` → navega). Nenhuma regra de negócio mora no `.vue`.
- O snapshot é **imutável**: toda mutação passa por `setEditing` com nova
  instância (copyWith). Proibido mutar o registro em edição no lugar.

> A confirmação de cancelar é **distinta** da guarda de navegação (sair da tela
> por voltar/rota com alterações não salvas → "Descartar alterações?"). São dois
> portões para a mesma proteção, em affordances diferentes (botão Cancelar × sair
> da tela); ambos só aparecem com o registro *dirty*.

## Consequências

### Benefícios

- Cancelar passa a ser **previsível e seguro** (desfaz e mostra o original).
- Elimina duplicação: o snapshot/cancelar vive **uma vez** na `BaseCrudStore`.
- Mantém o **contexto** do usuário na edição (não perde o lugar).
- Reaproveitável por todos os módulos e produtos da Noturno sem código novo.

### Riscos

- O `isDirty` e o snapshot dependem de comparação por valor (serialização) — para
  models muito grandes, avaliar custo (hoje desprezível).
- Stores que mantinham `editing`/`original` próprios devem **migrar** para o
  estado da base para não divergir (feito no módulo de Usuários do EspaçoN).

### Impactos

- `shared/stores` (`base-crud-store.ts`) — novo estado/ações de edição;
- `presentation/stores` de cada módulo — passam a delegar a edição à base;
- `presentation/pages` — "Cancelar" reage a `stay`/`leave`;
- documentação de UI (Design System §9.2 e `docs/ui`).

## Quando revisar esta decisão

- Se alguma tela precisar **cancelar sem confirmação** mesmo com alterações (raro
  — exigiria justificativa de UX), ou confirmar **sempre** (mesmo sem alterações).
- Quando houver edição **multi-etapas/wizard**, que pode exigir snapshots
  parciais por etapa.

## Relação com outros ADRs

Refina o padrão de CRUD do template (hierarquia de stores). Complementa o
ADR-002 (scroll infinito), que também altera a `BaseCrudStore`. Não há relação de
substituição com ADRs anteriores.

## Observações

A presentation **nunca** interpreta a regra de negócio do cancelar: ela só
traduz `stay`/`leave` em "permanecer" ou "navegar". Mantém o fluxo
Page → Store → UseCase intacto.
