# Arquitetura Noturno Web

## Objetivo

Este repositório é o template base para todos os projetos web da Noturno Softwares.

## Princípios

- Frontend e backend são projetos independentes.
- O frontend nunca implementa backend.
- O Next.js é utilizado apenas para interface e roteamento.
- APIs são externas ao frontend.
- Todo módulo deve iniciar com dados mockados.
- Integrações reais são adicionadas posteriormente.
- Todo acesso a dados deve passar por UseCase e Repository.
- O sistema deve suportar múltiplos provedores externos.
- Toda implementação deve possuir documentação.

## Estrutura

domain
data
presentation
shared

## Fluxo

Page
→ Store
→ UseCase
→ Repository
→ Provider
