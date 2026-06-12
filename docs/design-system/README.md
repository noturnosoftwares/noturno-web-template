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

**Identidade (avatar de pessoas, §8.11).** Conjunto curado `--color-avatar-1..5`
(laranja, verde, verde-claro, azul-claro, azul), tint @18% de fundo + cor cheia de texto.
Todos derivados dos primitivos `noturno-*`; **não** inclui vermelho/amarelo (reservados a
erro/alerta). A escolha por pessoa é **determinística** (hash do nome).

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

**Fonte única do espaçamento de página: `PageContainer`.** O padding de página
(`space-5`→`space-6`) e a largura de leitura **não** são redefinidos por tela —
vêm do shared widget `PageContainer`. O gutter é **enxuto** em relação à sidebar
(margem lateral curta, sem desperdício de área útil). Toda tela dentro do
`AppShell` (pesquisa, cadastro **e** detalhe) é envolvida por ele, com a **mesma**
margem lateral e a **mesma** largura máxima. Proibido escrever
`p-5/lg:p-6 + mx-auto max-w-[…]` "na mão" por página (gera margens divergentes
entre pesquisa e cadastro — anti-padrão §12). Ver `docs/ui` §1.

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

**Inventário canônico** (nomes reais em `shared/widgets` — todo projeto Noturno entrega
este conjunto; a spec abaixo descreve cada um):

| Componente        | Arquivo                 | Papel                                    |
| ----------------- | ----------------------- | ---------------------------------------- |
| `BaseButton`      | `base-button.vue`       | Botão (primary/danger/neutral/icon)      |
| `BaseCard`        | `base-card.vue`         | Superfície/card padrão                   |
| `StatusBadge`     | `status-badge.vue`      | Situação (cor + ícone + rótulo)          |
| `EmptyState`      | `empty-state.vue`       | Vazio com `tone` (accent/danger/muted)   |
| `FormSkeleton`    | `form-skeleton.vue`     | Placeholder de detalhe/form ao carregar (§10.11) |
| `SearchField`     | `search-field.vue`      | Busca (Enter; ícone sem sobrepor)        |
| `LookupField`     | `lookup-field.vue`      | Referência (FK) **somente-leitura** que abre o cadastro em modo seleção (ADR-003) — **default** |
| `Country/State/CityLookupField` | `*-lookup-field.vue` | `LookupField` por entidade do módulo `locations` (abrir+filtrar encapsulados) |
| `SearchLookupField` | `search-lookup-field.vue` | Referência **type-to-search** inline — caso pontual, **não** é o padrão de FK |
| `RecordCodeBadge` | `record-code-badge.vue` | Código do registro (`Novo` / `Cód. NNNNN`) — §8.11 |
| `InitialsAvatar`  | `initials-avatar.vue`   | Avatar de iniciais para pessoas (cor por nome) — §8.11 |
| `FormField`       | `form-field.vue`        | Wrapper label → controle → hint/erro     |
| `BaseTextField`   | `base-text-field.vue`   | Input de texto (FormField embutido)      |
| `MaskedField`     | `masked-field.vue`      | Input com **máscara** (CPF/CEP/telefone) + `searchable` opcional |
| `DateField`       | `date-field.vue`        | **Data**: calendário + digitação `dd/mm/aaaa` (v-model ISO) |
| `BaseSelect`      | `base-select.vue`       | Seleção discreta (mesma anatomia do campo) |
| `FormSection`     | `form-section.vue`      | Grupo de campos por contexto             |
| `BaseDataTable`   | `base-data-table.vue`   | Grid de leitura (sort/paginação client)  |
| `BasePagination`  | `base-pagination.vue`   | Paginação                                |
| `ConfirmDialog`   | `confirm-dialog.vue`    | Diálogo por finalidade (`purpose`)       |
| `BaseToast`       | `base-toast.vue`        | Toast (top-right; chrome §8.8)           |
| `StickyActionBar` | `sticky-action-bar.vue` | Rodapé de ação dirty-aware (§10.10)      |
| `AppShell`        | módulo `home`           | Casca sidebar + topbar                   |

> O `BaseField`/`BaseDialog` da spec original são entregues como `FormField`+`BaseTextField`
> e `ConfirmDialog` (finalidade via `purpose`). Booleanos usam **`Switch`** (§8.10).

### 8.1 Campo (`BaseField` e derivados)

Anatomia: **label (em cima)** → **controle** → **hint/erro (embaixo)**.

| Estado     | Borda             | Texto/Extra                                   |
| ---------- | ----------------- | --------------------------------------------- |
| Padrão     | `border-default`  | label `text-secondary`/`text-primary`; placeholder `text-muted` |
| Hover      | `border-strong`   | —                                             |
| Foco       | `border-focus` (dourado) + anel suave | —                         |
| Erro       | `feedback-danger` | helper em `feedback-danger` + ícone de erro   |
| Desabilitado | `border-subtle` | opacidade .4, sem cursor                       |

* Fundo: `surface-1`; raio `radius-md`; **altura 40px** — **igual para todos os
  campos** (texto, busca, select, lookup). Campos lado a lado **nunca** podem ter
  alturas/bordas diferentes. Controles de terceiros (PrimeVue `Select`, etc.) são
  embrulhados num componente-base (`BaseSelect`) que força essa anatomia — **nunca**
  usar o controle cru na tela.
* **Borda discreta por padrão.** Em repouso o campo usa `border-subtle` (não a borda
  cheia); hover sobe para `border-default`. Foco = **borda dourada + anel suave** (ring
  de baixa opacidade que abraça a borda). **Proibido o realce duplo** (outline deslocado
  *somado* à borda colorida) — lê como "borda dupla/grossa". É **um** realço, não dois.
* **Ícone dentro do campo nunca sobrepõe o texto.** Se houver ícone à esquerda (ex.: busca),
  o input recebe `padding-left = largura do ícone + space-3`. *(Bug visto na tela de Usuários.)*
* **Ícone interno fica sempre centralizado na vertical** (`top: 50%`, translate -50%),
  encostado na borda correspondente (esquerda p/ busca, direita p/ calendário/limpar) —
  **nunca** alinhado ao topo ou ao rodapé do campo. *(Bug visto no `DateField`: ícone do
  calendário caía no canto inferior direito.)*
* **Painel aberto do `BaseSelect` (overlay).** O dropdown é superfície de overlay
  (`surface-3`/`elev-3`, borda + raio + sombra suave) e **cada opção tem o mesmo
  recuo horizontal do campo** (`padding-left` alinhado ao texto/placeholder e ao
  hint) — itens **nunca** colados na borda esquerda. Opção sob o cursor/foco recebe
  realce tonal; a selecionada, o tint accent. Aplicado via `pt` (overlay/list/option),
  não no controle cru.
* Derivados padronizados em `shared/widgets`: `SearchField`, `BaseSelect`, `LookupField`,
  `MoneyField`, `DateField`, `CpfField`, `CnpjField`, `CepField`, `PhoneField`. Formatação/
  validação vêm de `shared/extensions` — nunca recodificar máscara por tela.
* **Ordem lógica dos campos.** Os campos seguem a **ordem natural de leitura/
  preenchimento** (ex.: nome → login → e-mail; em barras de pesquisa: termo →
  filtros). **Ações (botões) vêm sempre ao final** (à direita / depois dos campos)
  — ex.: `[busca] [filtro situação] [Buscar] [Limpar]`, nunca um filtro depois do
  botão de ação. A ordem de **Tab** acompanha (campo → campo → ação).
* **Booleano (sim/não, ativo/inativo, habilitar/desabilitar) é sempre um `Switch`** — nunca
  checkbox, select de duas opções ("Sim"/"Não") ou par de rádios. O switch comunica
  estado on/off de imediato e é operável por teclado.
* **Dado formatado usa máscara (`MaskedField`).** CPF, CEP, telefone/celular **não** são
  texto livre: usam máscara (PrimeVue `InputMask`, `unmask`→dígitos no model). Nunca pedir
  ao usuário que digite pontuação à mão nem validar formato "na unha".
* **Data usa `DateField`, nunca `<input type="date">` nativo.** O nativo é péssimo para
  **digitar**. O `DateField` é **`InputMask` (`dd/mm/aaaa`) + calendário** (`DatePicker`
  inline num `Popover`, aberto pelo ícone à direita): a digitação é **mascarada** e, ao
  completar uma data válida, **a seleção do calendário acompanha o que foi digitado**;
  escolher no calendário preenche o input. O model trafega em ISO. *(O `DatePicker` cru
  sozinho não mascara nem segue a digitação — por isso a composição.)*
* **E-mail é sempre gravado em minúsculas.** Todo campo de e-mail normaliza para lowercase
  no `set` (`normalizeEmail` de `shared/extensions`) e **também no mapper `fromJson`** —
  ou seja, **inclusive vindo do backend**. Evita duplicidade e divergência de login;
  e-mail é case-insensitive na prática.
* **Campo que pesquisa / virá do backend nasce como busca.** Todo dado que **consulta**
  algo (cidade, representante) ou que **virá de uma API no futuro** (endereço/mapa, CEP)
  é um **campo de busca** — `LookupField` (abre o cadastro em modo seleção) quando é uma
  **referência (FK)**, ou o gatilho `searchable` do `MaskedField`/campo de texto (mostra o
  ícone de busca e emite `search`, hoje "em breve") para integrações futuras (endereço/mapa,
  CEP). **Proibido** nascer como texto puro um campo que claramente vai pesquisar.
* **Campo de referência a outro registro (FK)** nunca é input de ID cru, select gigante,
  nem **digitação livre**. **Regra de produto: o padrão é o `LookupField`** — um **campo de
  pesquisa somente-leitura** que, ao acionar, **abre a tela/módulo daquele cadastro em modo
  seleção** (canal `shared/selection`, ADR-003) e recebe o registro de volta. **Não se digita
  no campo** — a pesquisa acontece na tela de listagem. Vale para Cidade, Estado/UF, País,
  Representante, Cliente, Fornecedor, Funcionário, Operador…
  * **`LookupField`** (**default**) — lupa + registro selecionado (ou placeholder); clicar
    emite `open`. Enquanto o cadastro-alvo **ainda não tem tela**, o campo já nasce assim e o
    `open` informa "em breve" (ou abre um placeholder) — o formato certo desde já, a tela é
    plugada depois. Widgets prontos por entidade: `CityLookupField`/`StateLookupField`/
    `CountryLookupField` (módulo `locations`), que encapsulam abrir a listagem + filtro.
  * **`SearchLookupField`** (autocomplete type-to-search) — **não é o padrão de FK**. Existe
    só para casos pontuais onde digitar inline faz sentido e não há tela de listagem própria;
    referência de cadastro usa `LookupField`.

  Na dúvida sobre qual usar (ou se um dado é "lista/lookup" ou outra coisa), **pergunte antes**
  de implementar — não assuma. *(Erro recorrente: tratar FK como autocomplete inline; o
  esperado é abrir o cadastro.)*
  * **Ida-e-volta do modo seleção** (obrigatório): abrir a listagem **não** é "sair da edição"
    — a guarda de navegação **não** pode pedir descarte ao ir para `?mode=select` (libere essa
    rota no `onBeforeRouteLeave`). E ao **voltar**, devolva o **foco** (e role até) o campo que
    disparou a busca — registre o `focusId` na requisição e use `restoreSelectionFocus` no
    retorno. O usuário nunca deve perder o lugar na tela.

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

### 8.6 Carregamento de listagem — scroll infinito (`BaseDataTable`) e `BasePagination`

**Padrão das listagens de pesquisa: scroll infinito (ADR-002).** O grid carrega
em **lotes de 30** e, ao aproximar do fim, pede o próximo lote automaticamente
(sentinela via `IntersectionObserver`), com um **indicador de carregamento no
rodapé** enquanto houver dados (`hasMore`). **Sem** controles/numeração de
página. Estado na `BaseCrudStore` (`loadPage`/`loadMore`/`hasMore`/`loadingMore`);
o grid emite `load-more`, a página liga ao `store.loadNext()`.

`BasePagination` (primeiro/anterior/página/próximo/último + faixa "1–20 de 134")
**não foi revogado** — permanece para casos específicos que exijam navegação
determinística por página (ex.: "ir para a página X") —, mas **não é o padrão**
das pesquisas. Ver ADR-002 e `docs/ui` §2.

### 8.7 Diálogo (`BaseDialog`)

Overlay com scrim + painel `elev-3`, `radius-xl`. Header (título + **cor da finalidade**),
corpo, rodapé com ações. A **cor representa a finalidade**:

| Finalidade        | Accent do diálogo | Botão de confirmação        |
| ----------------- | ----------------- | --------------------------- |
| Exclusão/perigo   | `feedback-danger` | `danger`                    |
| Confirmação geral | `accent`          | `primary`                   |
| Sucesso/positivo  | `feedback-success`| `primary`                   |
| Informação        | `feedback-info`   | `neutral`/`primary`         |

Entregue como **`ConfirmDialog`** (finalidade via prop `purpose`). Layout padrão:

* **Ícone pequeno** (círculo sutil ~28px) **alinhado ao título** na mesma linha — não ao
  bloco todo. Título `text-h3`/semibold; mensagem `text-body` em `content-muted`.
* **Padding e espaçamento padronizados** (corpo `space-5`, gaps `space-4` entre título,
  texto e ações).
* **Botões à direita, mesma altura/largura** (`min-width` comum): Cancelar `neutral`
  (ghost/outline) + confirmação na cor da finalidade. **Largura contida** (~24rem, com
  `max-width` responsiva).

### 8.8 Toast (`BaseToast`)

* **Posição: canto superior direito**, com slide+fade e **margem do canto** (não colado na
  borda). Fica do lado oposto à **barra de busca** (que vive à esquerda do header), para
  **nunca cobri-la**.
* **Chrome obrigatório** (não é um bloco de texto solto): superfície `elev-3` com **borda**,
  `radius-card`, **barra de cor à esquerda** por severidade, **ícone** num círculo tonal,
  **título** (`summary`) + **mensagem** (`detail`) e botão **fechar**. Largura contida
  (~22rem), conteúdo com respiro — nunca largo, sem margem e sem título.
* Tipos success/info/warning/danger (cor + ícone + mensagem). Auto-dismiss ~4s,
  dispensável, **empilhável com folga**: toasts simultâneos têm **margem entre si**
  (nunca colados) — aplicada no wrapper de mensagem (`pt.message`), não no chrome.
* **Dedupe por conteúdo (obrigatório).** Um toast cujo conteúdo idêntico
  (severity + título + mensagem) **já está visível** não é reaberto — clicar
  várias vezes em "Salvar" (ou em qualquer gatilho) **não** empilha cópias. A
  chave se libera quando o toast anterior se dispensa (mesmo `life`). Disparar
  **sempre** pelo wrapper `useAppToast().add(...)` (`shared/widgets`), nunca pelo
  `useToast()` cru do PrimeVue.
* **Disparo pós-ação:** ex. após excluir → "Usuário excluído."; após salvar → "Salvo."
* **Falha de salvamento sempre vira toast.** Em qualquer erro de save (validação
  **ou** API), além do informativo geral no topo + erros por campo, dispare um
  toast `error` com a **mesma mensagem geral** (formulários longos podem ter o
  banner fora da viewport). Ver §9.2/§10.10 e `docs/ui` §3.

### 8.9 Shell de aplicação (`AppShell`)

Toda tela autenticada vive dentro do shell: **sidebar** (recolhível) + **topbar**
(busca Ctrl+K, notificações, ajuda, perfil) + área de conteúdo em `surface-canvas`.
**Proibido** página solta full-bleed dentro do app. *(Quebra vista na tela de Usuários.)*

* **Sidebar com largura ajustável.** O usuário **arrasta a borda direita** para
  acomodar rótulos longos (alça fina com `cursor-col-resize`, realce accent ao
  passar/arrastar). A largura é **clampada** a um intervalo confortável e
  **persistida** entre sessões (storage). Durante o arrasto, **sem** transição de
  largura (acompanha o cursor); fora dele, transição suave. O **modo recolhido** (só
  ícones) ignora a largura e desliga a alça. Rótulo que ainda exceda usa **ellipsis**
  (uma linha), nunca corta no meio da palavra sem indicação.

A **sidebar** é densa (padding vertical enxuto), rótulos em **uma linha** com ellipsis
(`truncate`), ícone+texto alinhados à esquerda, sub-itens indentados por guia à esquerda
(`border-l`), realce do item ativo **limpo** (tint `accent-soft` + texto/ícone accent) e
scrollbar fina da identidade.

### 8.10 Switch, Seção de formulário e Rodapé de ação

* **Switch (`ToggleSwitch` no preset Noturno):** o controle on/off de **todo booleano**
  (§8.1/9.2). Thumb centrado na vertical, encostando à direita (ON) / esquerda (OFF) — ver
  a correção obrigatória de alinhamento em §13.3.
* **`FormSection`:** agrupa campos por contexto num card com título/descrição (§9.2) —
  substitui o `<fieldset>` "na mão".
* **`StickyActionBar` (rodapé de ação):** Salvar/Cancelar **dirty-aware**, **sólido e FORA
  da área de rolagem** (não flutua nem sobrepõe o conteúdo) — ver §10.10.

### 8.11 Identidade do registro — código e avatar de iniciais

* **Código em toda tela (`RecordCodeBadge`).** Todo registro **expressa seu código**:
  - nas **listas de pesquisa**, uma coluna **"Cód."** (a primeira, estreita);
  - no **cabeçalho do formulário**, o badge ao lado do título.
  Registro **novo** → pílula neutra **"Novo"**; existente → **`Cód. NNNNN`** (zero-pad,
  accent). É um afford­ance consistente de "onde estou / qual registro é este".
* **Avatar de iniciais para pessoas (`InitialsAvatar`).** Nas **listas de pessoas**
  (funcionários, usuários e, no futuro, clientes/fornecedores) a célula de **nome** leva
  um avatar circular com as **iniciais** (primeiro + último nome; ignora conectivos
  "da/de/dos"). A **cor é determinística** pelo nome (mesma pessoa → mesma cor), escolhida
  de um **conjunto curado da paleta oficial** (tokens `--color-avatar-*`: laranja, verdes e
  azuis), **sem vermelho/amarelo** (que carregam semântica de erro/alerta). Tint suave de
  fundo + inicial na cor cheia, anel sutil — elegante e legível no dark. **Não** usar
  avatar em registros que não são pessoas (perfil, operador de caixa): só o código.
* **Cores fora da paleta continuam proibidas.** O avatar **não** abre exceção: as cores de
  identidade são tokens derivados dos primitivos `noturno-*` (§2), não hex avulso.

---

## 9. Padrões de Tela

### 9.1 Página de pesquisa

* **Abre vazia** — nunca carrega todos os dados ao entrar. No lugar do grid, mostra uma
  **arte de empty-state** convidando à busca: ícone que **remete ao domínio** (usuários →
  `pi-users`; clientes → `pi-building`; contratos → `pi-file`; chamados → `pi-headphones`)
  com **pulse gentil**, e copy amigável **não repetitiva**. Banco de variações sugerido:
  "Pronto para pesquisar.", "Comece pela busca acima.", "Busque para ver os resultados.",
  "Digite e pressione Enter."
* **Dois vazios distintos:** (1) **convite à busca** — tom `accent`, ícone do domínio,
  pulse; (2) **"nada encontrado"** — tom **`danger`** (o propósito é "não achei", não um
  alerta amarelo), ícone de busca-sem-resultado, que **destaca o termo pesquisado em
  vermelho** (token `danger` = `#FF2626`, nunca hex avulso), entre aspas
  (ex.: *Nenhum resultado para "jose"*), **e oferece o botão "Limpar pesquisa"**
  abaixo — que **limpa termo + filtros** e **recarrega a lista completa**. Tudo via
  `EmptyState` compartilhado (slot `title` para o destaque, slot padrão para o botão).
* **Busca por Enter ou botão** — **nunca** busca incremental no backend a cada tecla.
  Se os dados já estiverem em **cache**, a digitação filtra no cliente.
* **Filtros sempre que possível**; **ordenação**; **carregamento por scroll infinito**
  (lote de 30 — §8.6/ADR-002), nunca controles de página.
* **Filtros não requisitam o backend ao mudar.** Em telas de CRUD/pesquisa com
  parâmetros + botão buscar, **alterar um filtro filtra o cache localmente** (sobre
  os dados já carregados) — **sem** nova requisição. Os filtros servem como
  **parâmetros da requisição**: quando há uma busca (Enter/Buscar) ou carga
  (scroll), os valores atuais dos filtros vão no request. Ex.: já listei usuários;
  mudar "Situação" filtra a lista carregada na hora; a próxima busca leva
  "Situação" como parâmetro ao backend. (A **busca por termo** é a ação que dispara
  o request — §9.1; os **filtros** apenas refinam/parametrizam.)
* Quando fizer sentido, **cards de situação** no topo com dados importantes do resultado
  (totais, críticos, etc.).
* Sem ações inline no grid (ver 8.5).
* **Preservar o contexto ao voltar.** Abrir um registro e retornar **não pode zerar** a
  pesquisa: o termo, os filtros e os resultados continuam. Melhor ainda — ao voltar,
  **reexecuta a busca** com os filtros atuais para **refletir inclusões/edições/exclusões**.
  O estado da busca vive no **store** (não em ref local da page, que morre ao desmontar).

### 9.2 Formulários e CRUD

* **Agrupar campos por contexto** em seções/cards com título.
* **Não poluir:** no máximo 2 (às vezes 3) campos por linha no desktop; analisar a
  disposição **antes** de implementar; campos longos ocupam a linha inteira.
* **Combobox/Select só para dados estáticos** (listas pequenas e fixas — ex.: papel, escopo,
  tipo). **Dado vindo do backend / referência a outro registro** (FK: Funcionário, Cliente,
  Cidade, Operador, **Perfil/modelo**…) usa **`LookupField`** — um **campo de busca (search)**: exibe o registro
  escolhido e, ao acionar, **abre a pesquisa que retorna o registro** (tela/diálogo de busca
  dedicado). **Não** é um listbox/autocomplete/select cravado nem um input de ID cru. O campo
  já nasce nesse formato mesmo que a tela de retorno ainda não exista. Filtros de listagem
  também usam **search fields**.
* **Booleanos são `Switch`** (ver §8.1): qualquer sim/não, ativo/inativo, habilitar/X.
* **Salvar/Cancelar só quando há alteração (dirty).** Em um form sem mudanças, não exiba a
  barra de ação — o usuário sai pelo voltar/cabeçalho. As ações aparecem **assim que há o
  que salvar**, evitando "salvar" sem efeito e cliques à toa (ver §10.10). Ações que não
  dependem de edição (ex.: **Excluir** em modo edição) ficam no **cabeçalho da entidade**,
  não na barra de salvar.
* **Confirmar antes** de: cancelar, pagar, excluir e qualquer ação
  destrutiva/irreversível.
* **Cancelar (ADR-001):** cancelar **descarta alterações**, então **confirma
  antes** quando o registro está *dirty* ("Cancelar alterações?", diálogo
  `danger`, com "Continuar editando"); sem alterações, cancela direto. **Após
  confirmar:** cancelar uma **edição** **restaura o registro original** (snapshot
  imutável na `BaseCrudStore`) e **permanece na tela de detalhe** — não volta para
  a pesquisa; cancelar um **registro novo** descarta e **volta para a listagem**.
  A regra mora na `BaseCrudStore` (`cancelEditing() → 'stay' | 'leave'`); a página
  pergunta a confirmação quando `isDirty` e só **reage** ao resultado.
* **Falha de salvamento → toast.** Em qualquer erro de save (validação ou API),
  mantenha o informativo geral no topo + erros por campo **e** dispare um toast
  `error` com a **mesma mensagem geral** (§8.8). Ver `docs/ui` §3.
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

* `Salvar`/`Cancelar` em **rodapé de ação**, exibido **somente quando há alteração não
  salva** (dirty). Sem mudanças, sem barra.
* O rodapé é **sólido e fica FORA da área de rolagem** — o conteúdo rola **acima** dele e
  **nunca passa por baixo**. **Proibido** barra flutuante/translúcida sobrepondo o conteúdo
  (pode esconder algo que o usuário quer ver). Ou rodapé fixo abaixo, ou nada.
* **Resumo de validação** no topo ao submeter com erros; **campos obrigatórios marcados**.
* Combina com a guarda de navegação de 9.2 (descartar não-salvo).

### 10.11 Abertura de detalhe/formulário — skeleton, nunca conteúdo anterior

**Regra:** uma tela de detalhe/edição **nunca** renderiza o registro **anterior**
enquanto carrega o novo. Abrir mostrando "sujeira" (dados do último registro,
piscando antes de trocar) é proibido — a experiência tem de ser limpa.

Causa comum: as stores de CRUD são **singletons**, então `editing` ainda guarda o
último registro ao abrir outro detalhe. Padrão obrigatório:

* **Limpar antes de carregar.** Ao entrar em edição, **zere o `editing`** (e rótulos
  resolvidos) **antes** da chamada assíncrona — no `loadForEdit` da store **e**
  **sincronamente no setup** da página (antes do 1º paint, pois `onMounted` roda
  *depois* do primeiro render). Em **registro novo**, inicialize o form em branco
  também no setup.
* **Skeleton enquanto carrega.** Com `editing` nulo + `loading`, a tela mostra o
  **`FormSkeleton`** (blocos pulsantes na mesma anatomia de seção/campo — par do
  skeleton de linhas da tabela em §8.5), nunca os campos vazios/velhos. Condição
  típica: `loading || (isEdit && !editing)`.
* **Retorno de seleção é exceção.** No *detour* de modo seleção (ADR-003) a edição é
  **preservada** (não limpa) — detecte isso **no setup** para não reinicializar o
  formulário em andamento.
* **Cabeçalho/ações coerentes.** Botões que dependem do registro (Excluir/Inativar)
  só aparecem **com `editing`** carregado — não sobre o skeleton.

O mesmo princípio vale para **listas**: durante o (re)carregamento o grid mostra o
**skeleton de linhas** (§8.5), não os itens da busca anterior.

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
* **Filtro que dispara requisição ao backend ao mudar** (deve filtrar o cache
  localmente; só vai ao backend como parâmetro na próxima busca/carga).
* Status comunicado **só por cor**.
* Sombra como único recurso de profundidade no escuro.
* Cor/medida fora dos tokens (hex/px avulso).
* Duas telas renderizando o mesmo elemento de formas diferentes.
* **Todas as ações sempre visíveis**, ignorando o estado do registro.
* **Grid financeiro sem linha de totais.**
* Data/moeda/número **fora do pt-BR** ou formatado "na mão" na tela.
* Form longo **sem barra de ação fixa** (Salvar/Cancelar perdidos no rolar).
* Edição em massa **sem navegação por teclado**.
* **Realce de foco duplo** no campo (outline deslocado + borda colorida) — vira "borda dupla".
* **Campo de referência (FK) como input de ID cru ou select gigante** (use `LookupField`).
* **Booleano que não é `Switch`** (checkbox/"Sim·Não"/rádios para on·off).
* **Salvar/Cancelar visíveis sem nenhuma alteração** (barra de ação deve ser dirty-aware).
* **Rodapé de ação flutuante/translúcido sobrepondo o conteúdo** (deve ficar FORA da rolagem).
* **Sair de inclusão/edição com alterações sem confirmar o descarte** (perde tudo em silêncio).
* **Cancelar uma edição/inclusão com alterações sem confirmar** (descarte silencioso — ADR-001).
* **Reference field como listbox/autocomplete cravado** em vez de campo de busca (search).
* **Pesquisa que zera ao voltar** de um registro (estado de busca tem que sobreviver).
* **Abrir detalhe/edição mostrando o registro ANTERIOR** enquanto carrega (flash de
  conteúdo sujo da store singleton) — limpe antes de carregar e mostre `FormSkeleton` (§10.11).
* **Botão que abre lista em modo seleção permitindo escolher registro inválido**
  (operador inativo, funcionário demitido) — respeitar o filtro de aceitação (ADR-003).
* **Itens do dropdown de `BaseSelect` colados na borda** (sem o recuo do campo) — §8.1.
* **Registro sem expressar o código** (lista sem coluna "Cód." ou form sem badge — §8.11).
* **Campo formatado (CPF/CEP/telefone) sem máscara**, ou **data em `<input type="date">`
  nativo** (ruim para digitar) em vez de `MaskedField`/`DateField` — §8.1.
* **Campo que pesquisa/virá de backend nascendo como texto puro** (sem `SearchLookupField`
  nem gatilho `searchable`) — §8.1.
* **Lista de pessoas sem avatar de iniciais**, ou avatar com cor fora dos tokens
  `--color-avatar-*` / com vermelho/amarelo (semântica de erro/alerta) — §8.11.

---

## 13. Implementação na stack (resumo)

### 13.1 Tokens (`src/app/globals.css`, Tailwind v4 `@theme`)

Primitivos `--color-noturno-*` (paleta oficial) + a camada **semântica**. Os nomes
semânticos são escolhidos para gerar utilitários Tailwind **sem duplicar o prefixo da
categoria** (evita `text-text-primary` / `border-border-default`):

| Papel       | Tokens (`--color-…`)                                    | Utilitário (exemplo)            |
| ----------- | ------------------------------------------------------- | ------------------------------- |
| Superfície  | `surface-canvas`, `surface-1`, `surface-2`, `surface-3` | `bg-surface-1`                  |
| Texto       | `content`, `content-soft`, `content-muted`, `on-accent` | `text-content-muted`, `text-on-accent` |
| Borda       | `line-subtle`, `line`, `line-strong`                    | `border-line`                   |
| Accent      | `accent`, `accent-hover`, `accent-soft`                 | `bg-accent`, `bg-accent-soft`   |
| Feedback    | `success` / `danger` / `warning` / `info` (+ `-soft` @14%) | `text-danger`, `bg-danger-soft` |

Raio: `--radius-field` (10px) → `rounded-field`; `--radius-card` (14px) → `rounded-card`;
`--radius-panel` (20px) → `rounded-panel`. Movimento: `--duration-fast/base/slow` + easings.
Helpers de classe: `.ds-focus-ring`, `.ds-card-lift`, `.ds-soft-pulse`, `.ds-rise-in`
(respeitam `prefers-reduced-motion`). Tema escuro via `.dark` no `<html>`.

> **Regra de nomenclatura:** a categoria **texto** vira `content*` e **borda** vira `line*` —
> nunca `text-*`/`border-*` no nome do token, senão o utilitário gerado duplica o prefixo.

### 13.2 Preset PrimeVue "Noturno" (`src/main.ts`)

`definePreset(Aura, …)` remapeia o tema para a paleta: `primary` na rampa do **dourado**
(`#ffb621` = 500, `#ff9500` = 600), `surface` na escada dark (até `#040404`) e `formField`
(borda discreta, raio 10px, foco dourado com anel suave). Assim **todo componente PrimeVue
nasce on-brand** — proibido estilizar PrimeVue por fora com cor crua; ajustar via tokens.

### 13.3 Correções obrigatórias do PrimeVue (universais)

Regras **não-layerizadas** em `globals.css` (vencem a layer `primevue`):

* **ToggleSwitch:** o CSS estrutural posiciona o thumb com `position:absolute; top:50%` mas
  não dá contexto de posicionamento à raiz → o thumb cai/desloca. Corrigir com
  `.p-toggleswitch{position:relative}` + `.p-toggleswitch .p-toggleswitch-handle{top:50%;margin-block-start:0;transform:translateY(-50%)}`.
* **Toast:** o wrapper padrão `.p-toast-message` traz fundo/borda/sombra por severidade que
  "vazam" atrás do card → zerar `background/border/box-shadow/border-radius`. O accent do
  card é um `border-l` (acompanha o raio), nunca um elemento posicionado em absoluto.

### 13.4 Demais

* **Componentes-base** em `src/shared/widgets` (inventário em §8); formatadores/validadores e
  localização pt-BR (`formatCurrency`/`formatDate`/`formatNumber`) em `src/shared/extensions`.
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
* [ ] Toasts no **topo-direito** após ações (sem cobrir a busca), com chrome completo (§8.8).
* [ ] Forms agrupados por contexto e sem poluição; booleanos em `Switch`; referências (FK) em `LookupField` (campo read-only que abre o cadastro em modo seleção); e-mail em lowercase.
* [ ] Estados loading/vazio/erro/sucesso previstos.
* [ ] Contraste e foco de teclado ok; responsivo.
* [ ] Animações sutis aplicadas; nada extravagante.
* [ ] Operável por teclado (Enter/Esc/Tab; atalhos onde fizer sentido) e densidade respeitando o usuário.
* [ ] Datas/moeda/números em pt-BR via helpers (sem formatação manual).
* [ ] Detalhe denso via master-detail/drawer ou página com header + abas; auditoria no Histórico.
* [ ] Ações contextuais por estado; lote via seleção; grid financeiro com totais; colunas/visões persistidas.
* [ ] Form longo com barra de ação fixa **dirty-aware** + resumo de validação + obrigatórios marcados.
* [ ] Campos com foco de realce **único** (sem borda dupla); pesquisa **preserva contexto** ao voltar.
* [ ] Switch com thumb centrado (§13.3); toast sem vazamento de cor nos cantos (§13.3).
* [ ] Correções obrigatórias do PrimeVue aplicadas (§13.3) e preset Noturno ativo (§13.2).
* [ ] Documentos com status visível e transições explícitas.
