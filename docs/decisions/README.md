# Architectural Decision Records (ADR)

## Objetivo

Registrar decisões arquiteturais importantes do projeto.

Nenhuma decisão arquitetural relevante deve ficar apenas na cabeça do desenvolvedor ou da IA.

Todo conhecimento importante deve permanecer documentado no repositório, permitindo que qualquer pessoa entenda, no futuro, por que determinada decisão foi tomada.

---

## Quando registrar uma decisão

Crie um ADR quando a decisão afetar arquitetura, padrões técnicos, organização do projeto, segurança, integração, manutenção ou evolução futura.

Exemplos:

* Adoção de biblioteca;
* Substituição de biblioteca;
* Mudança de stack;
* Mudança de arquitetura;
* Mudança de estrutura de pastas;
* Mudança de padrão de autenticação;
* Mudança de persistência de sessão;
* Mudança de Design System;
* Mudança de contrato de API;
* Mudança de gerenciamento de estado;
* Adoção de cache;
* Criação de package compartilhado;
* Definição de padrão para Repository;
* Definição de padrão para UseCase;
* Definição de estratégia de mocks;
* Escolha de biblioteca importante;
* Decisão provisória que deverá ser substituída no futuro.

---

## Quando não registrar uma decisão

Não crie ADR para mudanças pequenas, locais ou sem impacto arquitetural.

Exemplos:

* Ajuste de texto;
* Ajuste simples de layout;
* Correção visual pequena;
* Troca de margem, padding ou ícone;
* Correção de bug sem impacto arquitetural;
* Alteração simples em componente específico;
* Mudança que não afeta padrão, arquitetura, segurança, integração ou manutenção futura.

---

## Nome dos arquivos

Use o padrão:

```txt
ADR-000-titulo-da-decisao.md
```

Exemplos:

```txt
ADR-001-utilizar-mock-first.md
ADR-002-stack-web-vue-primevue-tailwind.md
ADR-003-gerenciamento-de-estado-com-pinia.md
ADR-004-persistencia-de-sessao-provisoria.md
```

---

## Status permitidos

Todo ADR deve possuir um status.

Status permitidos:

* Proposto;
* Aceito;
* Provisório;
* Substituído;
* Revogado.

### Proposto

Decisão ainda em análise.

### Aceito

Decisão aprovada e válida no projeto.

### Provisório

Decisão temporária, adotada para permitir avanço do projeto, mas que deverá ser revisada no futuro.

### Substituído

Decisão que foi trocada por outra decisão mais nova.

### Revogado

Decisão cancelada e que não deve mais ser seguida.

---

## Estrutura obrigatória do ADR

Todo ADR deve seguir esta estrutura:

```md
# ADR-000 — Título da decisão

## Status

Proposto / Aceito / Provisório / Substituído / Revogado

## Data

AAAA-MM-DD

## Contexto

Descreva o problema, necessidade ou situação que levou a esta decisão.

Explique o cenário atual, limitações existentes, dependências, pendências e o motivo pelo qual uma decisão precisa ser registrada.

## Opções avaliadas

Liste as alternativas consideradas.

### Opção 1 — Nome da opção

Explique a alternativa, seus benefícios e seus riscos.

### Opção 2 — Nome da opção

Explique a alternativa, seus benefícios e seus riscos.

### Opção escolhida — Nome da opção

Explique por que esta opção foi escolhida.

## Decisão

Descreva claramente qual decisão foi tomada.

A decisão deve ser objetiva e explicar:

- O que será feito;
- Qual padrão será adotado;
- Quais limites devem ser respeitados;
- Quais contratos, camadas ou módulos serão afetados;
- O que não deve ser feito.

## Consequências

### Benefícios

Liste os ganhos da decisão.

Exemplos:

- Reduz acoplamento;
- Facilita manutenção;
- Permite troca futura de implementação;
- Melhora organização do projeto;
- Evita repetição de código;
- Mantém padrão entre módulos.

### Riscos

Liste os riscos, limitações ou pontos de atenção.

Exemplos:

- Pode exigir revisão futura;
- Pode gerar trabalho inicial maior;
- Pode depender de implementação posterior;
- Pode ser uma solução provisória;
- Pode exigir cuidado para não ser usada fora do padrão definido.

### Impactos

Explique quais camadas, módulos, contratos ou arquivos são afetados.

Exemplos:

- `domain/models`;
- `domain/repositories`;
- `data/application`;
- `data/repositories`;
- `data/providers`;
- `data/mocks`;
- `data/adapters`;
- `presentation/pages`;
- `presentation/stores`;
- `presentation/widgets`;
- `shared/components`;
- `shared/design-system`;
- `shared/http`;
- `shared/result`;
- `router`.

## Quando revisar esta decisão

Defina claramente quando o ADR deverá ser revisado.

Exemplos:

- Quando a API real estiver disponível;
- Quando houver mudança de stack;
- Quando a decisão começar a limitar o projeto;
- Quando surgir uma alternativa melhor;
- Quando a implementação provisória for substituída;
- Quando houver impacto em segurança, performance ou manutenção.

## Relação com outros ADRs

Informe se esta decisão complementa, substitui ou refina outro ADR.

Exemplos:

- Este ADR complementa o ADR-001;
- Este ADR substitui o ADR-003;
- Este ADR refina o ADR-004, mas não o revoga;
- Não há relação com ADRs anteriores.

## Observações

Use este espaço para anotações adicionais, restrições ou cuidados futuros.
```

---

## Regra para IA

Toda decisão arquitetural relevante deve possuir um ADR.

Antes de criar um ADR, a IA deve avaliar se a mudança realmente tem impacto arquitetural.

A IA não deve criar ADR para ajustes pequenos de layout, textos, correções simples ou mudanças sem impacto arquitetural.

Quando criar um ADR, a IA deve seguir obrigatoriamente a estrutura definida neste documento.

O objetivo é permitir que qualquer pessoa entenda por que algo foi feito, mesmo anos depois.
