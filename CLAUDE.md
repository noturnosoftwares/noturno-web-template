# Noturno Web Template

## Sobre o Projeto

Este repositório representa o padrão arquitetural adotado pela Noturno Softwares para desenvolvimento de aplicações web.

Toda implementação deve respeitar este documento.

A IA não define arquitetura.

A arquitetura é definida por Glenio e pela Noturno Softwares.

---

# Princípios

* Frontend e Backend são projetos independentes.
* O frontend nunca implementa backend.
* O frontend nunca acessa banco de dados diretamente.
* Toda comunicação deve ocorrer através de APIs REST externas.
* O sistema deve permitir múltiplos provedores externos.
* Todo módulo deve iniciar utilizando dados mockados.
* A integração real ocorre posteriormente.
* Todo código deve ser preparado para manutenção por equipes independentes.

---

# Tecnologias

## Frontend

* React
* Next.js
* TypeScript

## Backend

O backend não faz parte deste projeto.

Pode ser:

* Delphi / TMS XData
* Node.js
* NestJS
* Go
* Outros provedores REST

O frontend nunca deve assumir detalhes de implementação do backend.

---

# Estrutura dos Módulos

Todo módulo deve seguir obrigatoriamente a estrutura:

src/modules/{module}

domain

* models
* enums
* repositories

data

* application
* repositories
* providers
* mocks

presentation

* pages
* stores
* widgets

---

# Responsabilidades

## Domain

Responsável por:

* Models
* Enums
* Contratos de Repository

Não deve conter implementação de API.

---

## Data/Application

Responsável por:

* UseCases
* Regras de orquestração
* Fluxos da aplicação

Exemplos:

* GetCustomers
* SaveCustomer
* DeleteCustomer

---

## Data/Repositories

Responsável por implementar os contratos definidos em domain.

---

## Data/Providers

Responsável pela comunicação com provedores externos.

Exemplos:

* ERPProvider
* MercadoLivreProvider
* OpenAIProvider
* AnthropicProvider

---

## Data/Mocks

Responsável por fornecer dados simulados durante o desenvolvimento inicial.

Todo novo módulo deve iniciar utilizando mocks.

---

## Presentation

Responsável por:

* Pages
* Stores
* Widgets

A camada de apresentação nunca deve acessar APIs diretamente.

---

# Fluxo Obrigatório

Page

→ Store

→ Application

→ Repository

→ Provider

Nunca quebrar este fluxo.

---

# Ciclo de Vida e Instâncias

Antes de criar services, providers, repositories, stores ou controllers, a IA deve analisar se a classe deve ser:

- Instância única / singleton
- Instância por módulo
- Instância por tela
- Instância por execução de usecase
- Instância temporária

A decisão nunca deve ser automática.

## Quando considerar singleton

Singleton pode ser considerado para:

- Configuração global
- Cliente HTTP compartilhado
- Controle de autenticação
- Cache global
- Serviços sem estado
- Providers reutilizados por todo o sistema

## Quando evitar singleton

Evitar singleton quando houver:

- Estado específico de tela
- Dados temporários
- Filtros de listagem
- Formulários
- Estado de edição
- Risco de vazar dados entre módulos
- Necessidade de múltiplas instâncias independentes

## Regra

Sempre justificar a escolha do ciclo de vida da instância em implementações relevantes.

---

# Integrações

Todo projeto deve permitir troca de provedores sem impacto na camada de apresentação.

Utilizar sempre Repository para abstração.

Nunca acoplar a interface diretamente a um fornecedor.

---

# Documentação

Toda funcionalidade relevante deve possuir documentação.

Toda funcionalidade complexa deve possuir especificação em:

docs/specifications

Toda decisão arquitetural deve ser registrada em:

docs/decisions

---

# Desenvolvimento Assistido por IA

Antes de implementar qualquer funcionalidade a IA deve:

1. Analisar a arquitetura existente.
2. Respeitar os padrões definidos neste documento.
3. Informar quais arquivos pretende criar ou alterar.
4. Explicar o fluxo da solução.
5. Somente depois realizar a implementação.

A IA não deve criar estruturas arquiteturais diferentes das definidas neste projeto.

---

# Restrições

Nunca criar:

* app/api
* API Routes
* Backend dentro do Next.js
* Acesso direto a banco
* Regras de negócio dentro de componentes React
* Chamadas HTTP diretamente em páginas ou widgets

---

# Objetivo

Criar aplicações web escaláveis, desacopladas, documentadas e preparadas para evolução contínua, mantendo os mesmos princípios arquiteturais utilizados nos projetos Flutter da Noturno Softwares.
