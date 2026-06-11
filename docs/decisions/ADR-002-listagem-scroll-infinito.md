# ADR-002 — Listagens com scroll infinito (lote de 30)

## Status

Aceito

## Data

2026-06-10

## Contexto

As listagens/pesquisas do template usavam **paginação clássica** (controles de
primeiro/anterior/página/próximo/último + faixa "1–20 de N"), com o
`BaseDataTable` paginando no cliente sobre o cache.

Em telas de gestão modernas, a navegação por número de página adiciona cliques e
quebra o fluxo de leitura: o usuário rola, encontra o fim e precisa mirar um
controle para ver mais. O padrão consolidado no mercado para listas de
escaneamento é o **scroll infinito** — carregar o próximo lote ao aproximar do
fim, com um indicador discreto no rodapé.

Como o comportamento de carga é **transversal a todo CRUD**, a decisão pertence à
camada corporativa (template) e deve nascer na `BaseCrudStore` + no grid
compartilhado, valendo para todos os módulos e produtos.

## Opções avaliadas

### Opção 1 — Manter paginação clássica

Conhecida e simples, mas com mais cliques e troca de contexto. Não acompanha o
padrão de leitura contínua das telas de gestão.

### Opção 2 — "Carregar mais" por botão

Scroll com botão manual no rodapé. Melhor que paginação, mas ainda exige uma ação
explícita a cada lote. Mantém-se como **fallback** acessível, não como padrão.

### Opção escolhida — Scroll infinito automático, lote de 30

Ao aproximar do fim da lista (sentinela observada no viewport), o grid pede o
**próximo lote** automaticamente e mostra um indicador de carregamento no rodapé
**enquanto houver dados**. Sem controles nem numeração de página. O lote é de
**30** registros por carga.

## Decisão

- **Listagens de pesquisa/listagem usam scroll infinito**, não paginação por
  número de página. Sem controles de página, sem faixa "1–N de M" como
  navegação.
- **Lote padrão de 30** por carga (`DEFAULT_PAGE_SIZE = 30` na `BaseCrudStore`).
- A `BaseCrudStore` é a fonte do estado: `loadPage` (primeiro lote, substitui a
  lista) e `loadMore` (anexa o próximo lote), além de `hasMore` e `loadingMore`.
  `loadMore` **não** aciona o skeleton geral — usa a flag própria `loadingMore`
  para o indicador de rodapé.
- O grid compartilhado (`BaseDataTable`) **emite `load-more`** via
  `IntersectionObserver` numa sentinela de rodapé (com folga de prefetch) e
  mostra o indicador enquanto `loadingMore && hasMore`. A ordenação permanece
  **client-side sobre o que já foi carregado**.
- A paginação por requisição continua existindo no contrato (`page`/`pageSize`):
  o backend (ou o repository mock) **pagina por lote**; muda apenas a UI de
  navegação, que passa a ser por rolagem.
- O componente `BasePagination` **não é revogado** — fica disponível para casos
  específicos que ainda exijam navegação por página —, mas **não é o padrão** das
  listagens de pesquisa.

## Consequências

### Benefícios

- Leitura contínua, menos cliques, menos troca de contexto.
- Estado de carga centralizado na base (reuso por todos os módulos/produtos).
- Mantém o contrato `page`/`pageSize` — troca por API real sem mexer na UI.

### Riscos

- **Ordenação/contagem**: a ordenação client-side ordena apenas o que já foi
  carregado; o "total" exibido vem do `total` do contrato (não da lista local).
  Para ordenação global em volumes grandes, migrar a ordenação para o backend.
- **Acessibilidade**: rolagem infinita exige cuidado com foco/teclado e com
  usuários que dependem de "fim de página". Manter um caminho acessível (ex.:
  sentinela com texto e, se necessário, fallback "carregar mais").
- **Footers fixos**: páginas com rodapé fixo não devem competir com a sentinela.

### Impactos

- `shared/stores` (`base-crud-store.ts`) — `loadMore`, `hasMore`, `loadingMore`,
  `DEFAULT_PAGE_SIZE`;
- `shared/widgets` (`base-data-table.vue`) — sentinela + `load-more`, sem
  paginação interna; `base-pagination.vue` mantido para casos específicos;
- `presentation/stores`/`pages` de cada módulo — expõem/consultam `loadNext`,
  `hasMore`, `loadingMore`;
- documentação de UI (Design System §8.6/§9.1 e `docs/ui`) e specs de grid.

## Quando revisar esta decisão

- Quando a API real estiver disponível e for preciso decidir **ordenação e
  filtro server-side** (a ordenação local deixa de bastar em grandes volumes).
- Se uma tela exigir navegação determinística por página (ex.: relatório com
  "ir para a página X") — aí o `BasePagination` volta a ser usado pontualmente.

## Relação com outros ADRs

Refina o padrão de grid/listagem do template. Complementa o ADR-001 (cancelar
edição), que também altera a `BaseCrudStore`. Não substitui ADRs anteriores;
**refina** (não revoga) os trechos de docs de UI e specs que mencionavam
"paginação".

## Observações

A presentation não implementa a regra de carga: o grid apenas **avisa** que
chegou ao fim (`load-more`) e a store decide buscar o próximo lote
(Page → Store → UseCase → Repository → Provider).
