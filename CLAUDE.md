# Noturno Web Template

## Papel da IA

A IA é uma executora técnica.

Ela não define arquitetura.

A arquitetura é definida por Glenio e pela Noturno Softwares.

Antes de implementar qualquer funcionalidade, a IA deve respeitar as regras deste documento.

---

# Arquitetura

## Frontend

- React
- Next.js
- TypeScript

## Backend

O backend é SEMPRE um projeto independente.

O frontend nunca implementa backend.

O frontend nunca implementa API interna.

Nunca utilizar:

- app/api
- API Routes
- Server Actions para regras de negócio
- Banco de dados diretamente no frontend

---

# Estrutura

Todo módulo deve seguir:

src/modules/{module}

  domain
    models
    enums
    repositories
    usecases

  data
    repositories
    providers
    mocks

  presentation
    pages
    stores
    widgets

---

# Fluxo

Page
→ Store
→ UseCase
→ Repository
→ Provider

Nunca acessar APIs diretamente de páginas ou componentes.

---

# Integrações

Todo sistema deve ser preparado para múltiplos provedores.

Exemplos:

- ERP Noturno
- Mercado Livre
- OpenAI
- Anthropic
- Outros provedores

Sempre utilizar Repository para abstração.

---

# Desenvolvimento

Todo módulo novo deve iniciar com:

- Mock Provider
- Dados mockados
- Casos de uso implementados
- Interface funcional

A integração real será realizada posteriormente.

---

# Documentação

Toda funcionalidade relevante deve possuir documentação.

Toda decisão arquitetural deve ser registrada em:

docs/decisions

Toda funcionalidade complexa deve possuir especificação em:

docs/specifications

---

# Princípios

- Separação entre frontend e backend
- Código desacoplado
- Baixo acoplamento entre provedores
- Reutilização de módulos
- Facilidade de manutenção
- Facilidade de testes
- Arquitetura preparada para equipes independentes
