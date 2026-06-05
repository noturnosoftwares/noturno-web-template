# Arquitetura Noturno Web

## Objetivo

Este repositório define o padrão arquitetural adotado pela Noturno Softwares para aplicações web.

O objetivo é criar sistemas:

* duradouros;
* desacoplados;
* escaláveis;
* testáveis;
* documentados;
* preparados para evolução contínua.

A arquitetura deve permitir manutenção por décadas.

---

# Princípios Arquiteturais

* Frontend separado do backend.
* Backend independente.
* APIs REST como meio de comunicação.
* Desenvolvimento iniciado com mocks.
* Integração real posteriormente.
* Reaproveitamento máximo de módulos.
* Monorepo.
* Packages compartilhados.
* Design System próprio.
* Documentação obrigatória.

---

# Estrutura

```txt
src
  app

  modules
    customer
    auth
    sales
    stock
    delivery
    doctorcar

  shared
```

---

# Estrutura de Módulos

```txt
module

domain
  models
  enums
  repositories

data
  application
  repositories
  providers
  mocks

presentation
  pages
  stores
  widgets
```

---

# Fluxo de Execução

```txt
Page
→ Store
→ Application
→ Repository
→ Provider
```

---

# Regras de Dependência

Presentation conhece:

* Store

Store conhece:

* Application

Application conhece:

* Repository

Repository conhece:

* Provider

Provider conhece:

* API externa

Fluxo inverso não é permitido.

---

# Reutilização

Antes de criar qualquer código novo deve ser analisado:

* shared widgets
* shared helpers
* extensions
* packages compartilhados
* módulos reutilizáveis

---

# Evolução

Toda implementação deve considerar:

* compatibilidade futura
* manutenção futura
* reutilização futura
* possibilidade de extração para package
* possibilidade de utilização por outros produtos da Noturno
