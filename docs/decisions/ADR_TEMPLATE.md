# ADR-000 — Título da decisão

## Status

Proposto / Aceito / Provisório / Substituído / Revogado

## Data

AAAA-MM-DD

## Contexto

Descreva o problema, necessidade ou situação que levou a esta decisão.

Explique o cenário atual, limitações existentes, dependências, pendências e o
motivo pelo qual uma decisão precisa ser registrada.

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
