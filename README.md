# noturno-web-template

Template base para projetos web da **Noturno Softwares**.

Este repositório define a arquitetura, os padrões e a documentação obrigatória
adotados pela Noturno em aplicações frontend. Ele serve como ponto de partida
para novos projetos e como referência durante o desenvolvimento assistido por IA.

---

## Stack

* Vue.js 3.5
* Vite
* TypeScript
* PrimeVue
* Tailwind CSS
* Pinia
* Vue Router

O backend não faz parte deste projeto. Toda comunicação externa acontece via
APIs REST. O desenvolvimento começa com dados mockados e a integração real é
adicionada depois.

---

## Documento principal

O arquivo [`CLAUDE.md`](./CLAUDE.md) é a fonte da verdade da arquitetura e das
regras do projeto. Ele é lido automaticamente pelo Claude Code e deve ser
respeitado integralmente em qualquer implementação.

---

## Estrutura de documentação

```txt
docs/
  architecture/     # padrão arquitetural do projeto
  decisions/        # Architectural Decision Records (ADR)
  specifications/   # especificação de funcionalidades complexas
  modules/          # documentação de cada módulo implementado
```

---

## Como usar

1. Use este repositório como base para um novo projeto web da Noturno.
2. Leia o `CLAUDE.md` antes de implementar qualquer funcionalidade.
3. Registre decisões arquiteturais relevantes em `docs/decisions` (ADR).
4. Especifique funcionalidades complexas em `docs/specifications` antes de codificar.
5. Documente cada módulo em `docs/modules`.

---

## Princípios

* Frontend separado e independente do backend.
* Arquitetura em camadas: domain, data, presentation.
* Fluxo unidirecional: Page → Store → Application → Repository → Provider.
* Mock-first: todo módulo começa com dados simulados.
* Documentação obrigatória.
* Foco em manutenção, escalabilidade e reuso por décadas.
