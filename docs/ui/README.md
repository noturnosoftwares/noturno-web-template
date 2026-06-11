# Padrões Operacionais de UI (complemento do Design System)

> **Autoridade.** A **lei visual** da Noturno é `docs/design-system/README.md`.
> Este documento **não a substitui nem a contraria** — apenas detalha, em nível
> operacional, alguns **padrões de comportamento de tela** transversais a todo
> CRUD (espaçamento de página, scroll infinito, toast de erro no salvamento e
> estado vazio de pesquisa). Onde houver conflito, vale o Design System.
>
> Cada padrão aqui referencia a seção correspondente do Design System (DS) e, quando
> a mudança tem impacto arquitetural, o ADR correspondente em `docs/decisions`.

---

## 1. Espaçamento de página — `PageContainer` (DS §4, §9)

**Problema corrigido.** Telas de **cadastro/detalhe** apresentavam margens
laterais **diferentes** das de **pesquisa** (larguras de conteúdo distintas e
padding repetido em cada página). Isso quebrava a consistência ("duas telas
renderizando o mesmo elemento de formas diferentes" — DS §12).

**Regra.** O espaçamento horizontal/responsivo das páginas de conteúdo tem **uma
fonte única**: o shared widget **`PageContainer`** (`src/shared/widgets`). Toda
tela dentro do `AppShell` (pesquisa, cadastro e detalhe) é envolvida por ele:

- padding padrão `space-6` (mobile) → `space-8` (desktop), **nunca** redefinido
  por tela;
- conteúdo **centralizado** com **largura máxima única** (pesquisa e cadastro
  alinham as margens);
- área de rolagem (`overflow-y-auto`) com a scrollbar da identidade.

`PageContainer` aceita `width="narrow"` (leitura mais estreita) e `width="full"`
(ocupa tudo) para casos raros; o padrão (`default`) unifica todas as telas.

**Proibido.** Reescrever `p-6/lg:p-8` + `mx-auto max-w-[…]` "na mão" em cada
página. Se precisar de um gutter diferente, ajuste o `PageContainer`, não a tela.

---

## 2. Listagens com scroll infinito (DS §8.5/§8.6/§9.1 · ADR-002)

**Regra.** Grids de **pesquisa/listagem** usam **scroll infinito**, não
paginação por número de página:

- carga em **lotes de 30** (`DEFAULT_PAGE_SIZE` na `BaseCrudStore`);
- ao aproximar do fim, o grid pede o próximo lote automaticamente (sentinela via
  `IntersectionObserver`) e mostra um **indicador de carregamento no rodapé**
  enquanto houver dados (`hasMore`);
- **sem** controles/numeração de página.

Estado na `BaseCrudStore`: `loadPage` (primeiro lote, substitui), `loadMore`
(anexa), `hasMore`, `loadingMore`. O grid compartilhado (`BaseDataTable`) emite
`load-more`; a página liga ao `store.loadNext()`.

`BasePagination` **não foi revogado** — permanece para casos específicos que
exijam navegação determinística por página —, mas **não é o padrão**.

> Detalhes, riscos (ordenação/contagem em grandes volumes) e plano de revisão:
> **ADR-002**.

---

## 3. Toast de erro no salvamento (DS §8.8, §9.2, §10.10)

**Regra.** Em **qualquer** falha de salvamento (validação **ou** API), a tela de
CRUD deve, ao mesmo tempo:

1. manter o **informativo geral no topo** (banner) + os **erros por campo**; e
2. **disparar um toast** com a **mesma mensagem geral**, usando o **serviço de
   toast já existente** (`useToast` / `BaseToast`, topo-direito — DS §8.8).

O toast garante feedback mesmo quando o banner está fora da viewport (formulários
longos com rolagem). A mensagem do toast é **idêntica** à do banner — não uma
segunda redação. Severidade `error`.

**Proibido.** Falhar o save "em silêncio" (sem toast) só porque o banner existe;
ou exibir no toast uma mensagem diferente da do topo.

---

## 4. Estado vazio na pesquisa — termo destacado + limpar (DS §9.1)

**Regra.** Quando a busca **não retorna resultados**, o empty-state (tom
`danger`, ícone de busca-sem-resultado) deve:

- **destacar o termo pesquisado em vermelho** (token `danger` = `#FF2626` da
  paleta — nunca hex avulso), entre aspas, dentro da mensagem
  (*Nenhum resultado para "jose"*); e
- oferecer, abaixo, um botão **"Limpar pesquisa"** que **limpa o termo e os
  filtros** e **recarrega a lista completa**.

Isso vem do `EmptyState` compartilhado (slot `title` para o destaque; slot padrão
para o botão) — nenhuma tela monta o vazio "na mão" (DS §8/§12).

**Proibido.** Mostrar "nada encontrado" sem indicar **o que** foi buscado, ou sem
um caminho de saída (limpar) para o usuário voltar à lista completa.

---

## Histórico deste documento

Estes padrões consolidam correções de UI transversais identificadas no produto
EspaçoN e promovidas ao template (camada corporativa). A `docs/ui` anterior
(orientada ao HelpDesk) é **refinada**, não revogada: a paleta e a lei visual
vivem no Design System; aqui ficam os **comportamentos operacionais** de CRUD.
