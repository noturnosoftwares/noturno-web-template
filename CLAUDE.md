# Noturno Web Template

## Papel da IA

A IA é uma executora técnica.

Ela não define arquitetura.

A arquitetura é definida por Glenio e pela Noturno Softwares.

Antes de implementar qualquer funcionalidade, a IA deve respeitar integralmente este documento.

---

# Perfil Arquitetural

Glenio possui mais de 23 anos de experiência com Delphi/Pascal e cerca de 3 anos com Flutter.

Os projetos da Noturno seguem:

* monorepo;
* módulos reutilizáveis;
* packages compartilhados;
* frontend separado do backend;
* backend REST/API independente;
* desenvolvimento iniciado com dados mockados;
* integração real feita posteriormente;
* Application/UseCase + Repository + Provider;
* documentação obrigatória;
* Design System próprio;
* foco em longo prazo, manutenção e escalabilidade.

A IA deve se adaptar ao padrão existente, nunca impor arquitetura diferente.

---

# Princípios Obrigatórios

* Frontend e backend são projetos independentes.
* O frontend nunca implementa backend.
* O frontend nunca acessa banco de dados diretamente.
* Toda comunicação externa deve ocorrer por APIs REST.
* O sistema deve permitir múltiplos provedores externos.
* Todo módulo novo deve iniciar com dados mockados.
* Integrações reais são adicionadas depois.
* Todo código deve ser preparado para manutenção por equipes independentes.
* Toda implementação relevante deve ser documentada.
* Nenhuma funcionalidade complexa deve ser implementada sem especificação.
* O objetivo não é apenas funcionar; é durar, escalar e ser fácil de manter.

---

# Tecnologias

## Frontend

- Vue.js 3.5
- Vite
- TypeScript
- PrimeVue UI
- Tailwind CSS
- Composition API
- Pinia
- Vue Router

## Backend

O backend não faz parte deste projeto.

Pode ser:

* Delphi / TMS XData
* Node.js
* NestJS
* Go
* Outros provedores REST

O frontend nunca deve assumir detalhes internos do backend.

---

# Restrições Absolutas

Nunca criar:

* `app/api`
* API Routes
* backend dentro do Next.js
* acesso direto a banco
* regras de negócio dentro de componentes React
* chamadas HTTP diretamente em pages
* chamadas HTTP diretamente em widgets
* cores fora da paleta oficial
* dependências externas sem justificativa
* arquitetura diferente da definida neste documento

---

# Estrutura dos Módulos

Todo módulo deve seguir obrigatoriamente:

```txt
src/modules/{module}
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

# Responsabilidades das Camadas

## Domain

Responsável por:

* models internos;
* enums;
* contratos de repositories.

Não deve conter:

* implementação de API;
* HTTP;
* regras visuais;
* dependência de framework de interface.

---

## Data/Application

Responsável por:

* UseCases;
* orquestração de fluxos;
* regras de aplicação;
* validação de fluxo;
* comunicação entre Store e Repository.

Exemplos:

* GetCustomers
* SaveCustomer
* DeleteCustomer
* BlockCustomer

---

## Data/Repositories

Responsável por:

* implementar contratos definidos em `domain/repositories`;
* decidir qual provider será usado;
* converter retorno de provider para resultado esperado pela application.

---

## Data/Providers

Responsável por:

* comunicação com APIs externas;
* consumo de backend REST;
* consumo de provedores externos.

Exemplos:

* ERPProvider
* MercadoLivreProvider
* OpenAIProvider
* AnthropicProvider
* MockProvider

---

## Data/Mocks

Responsável por:

* dados simulados;
* cenários iniciais;
* desenvolvimento antes da API real;
* testes manuais da interface.

Todo novo módulo deve iniciar usando mock.

---

## Presentation

Responsável por:

* pages;
* stores;
* widgets;
* componentes visuais específicos do módulo.

A presentation nunca deve acessar APIs diretamente.

---

# Fluxo Obrigatório

```txt
Page
→ Store
→ Application / UseCase
→ Repository
→ Provider
```

Nunca quebrar esse fluxo.

---

# Contratos de API e Models

A IA nunca deve assumir ou inventar o formato do JSON retornado pelo backend.

Antes de criar qualquer model, deve existir pelo menos um destes itens:

* JSON real de retorno da API;
* JSON real de envio para API;
* Swagger/OpenAPI;
* contrato documentado;
* exemplo fornecido pelo usuário.

Se não existir contrato, a IA deve solicitar o modelo do JSON antes de criar:

* model;
* mapper;
* fromJson;
* toJson;
* fromApi;
* toApi.

---

# Padrão de Models

## Idioma

Models do frontend devem ser escritos em inglês.

Mesmo que o backend retorne campos em português, o frontend deve manter nomenclatura própria em inglês.

Exemplo de backend:

```json
{
  "codigo": 1,
  "descricao": "Cliente teste",
  "situacao": "ATIVO"
}
```

Exemplo de frontend:

```ts
type Customer = {
  id: number;
  description: string;
  status: CustomerStatus;
};
```

A conversão deve acontecer na camada `data`, usando mappers, providers ou repositories.

---

## Imutabilidade

Models devem ser tratados como imutáveis.

Evitar alteração direta de propriedades depois da criação.

Preferir criar uma nova instância ao alterar dados.

---

## CopyWith

Quando o model representar estado ou puder sofrer alterações parciais, deve possuir função equivalente ao `copyWith` do Dart.

```ts
function copyCustomer(customer: Customer, changes: Partial<Customer>): Customer {
  return {
    ...customer,
    ...changes,
  };
}
```

---

## Serialização

Todo model relevante deve estar preparado para:

* `fromJson`
* `toJson`

ou equivalentes:

* `fromApi`
* `toApi`

Mesmo models simples devem ser preparados pensando em evolução futura.

---

# Padrão de Retorno Assíncrono

Toda chamada assíncrona de Application, UseCase, Repository ou Provider deve retornar um resultado padronizado, equivalente ao AsyncResult usado no Flutter.

```ts
export type AsyncResult<T> =
  | { success: true; data: T }
  | { success: false; error: AppError };
```

Regra:

* sucesso retorna `data`;
* falha retorna `error`;
* exceptions devem ser capturadas nas camadas de data/application;
* a presentation nunca deve tratar exception crua da API.

---

# Injeção de Dependência

O projeto deve utilizar injeção de dependência quando fizer sentido reaproveitar, trocar ou controlar instâncias.

Usar injeção de dependência para:

* repositories;
* providers;
* services;
* Auth/Login;
* HTTP Client;
* configurações globais;
* mocks e providers reais;
* dependências compartilhadas entre módulos.

Evitar injeção desnecessária em:

* funções puras simples;
* componentes locais;
* estados exclusivos de uma tela;
* objetos temporários sem reaproveitamento.

Fluxo desejado:

```txt
Store
→ Application
→ Repository injetado
→ Provider injetado
```

---

# Ciclo de Vida e Instâncias

Antes de criar services, providers, repositories, stores ou controllers, a IA deve analisar se a classe deve ser:

* instância única / singleton;
* instância por módulo;
* instância por tela;
* instância por execução de usecase;
* instância temporária.

A decisão nunca deve ser automática.

Singleton pode ser considerado para:

* configuração global;
* cliente HTTP compartilhado;
* controle de autenticação;
* cache global;
* serviços sem estado;
* providers reutilizados por todo o sistema.

Evitar singleton quando houver:

* estado específico de tela;
* dados temporários;
* filtros de listagem;
* formulários;
* estado de edição;
* risco de vazar dados entre módulos;
* necessidade de múltiplas instâncias independentes.

Sempre justificar a escolha do ciclo de vida da instância em implementações relevantes.

---

# Gerenciamento de Estado e Renderização

O projeto deve evitar estado espalhado diretamente em páginas grandes.

A IA deve priorizar stores ou controladores de estado para fluxos relevantes.

Princípios:

* evitar re-renderização desnecessária da tela inteira;
* manter pages simples;
* isolar widgets/componentes pequenos;
* estados locais só devem ser usados para comportamentos simples e isolados;
* fluxos de negócio devem ficar em stores/controllers;
* a presentation não deve conter regra de negócio.

Estado local pode ser usado apenas para coisas pequenas:

* abrir/fechar modal;
* controlar foco;
* alternar visual simples;
* estado interno de componente isolado.

Quando usar estado local, o componente deve ser pequeno para limitar renderização.

---

# Hierarquia de Stores

O projeto deve seguir hierarquia de stores semelhante ao padrão Flutter da Noturno.

```txt
BaseStore
  ↓
BaseCrudStore
  ↓
Store específica da tela
```

## BaseStore

Store base para estados comuns:

* loading;
* errorMessage;
* successMessage;
* isInitialized;
* isSaving;
* isDeleting;
* hasError.

## BaseCrudStore

Store base para operações CRUD.

Responsável por comportamentos comuns como:

* listagem;
* carregamento;
* criação;
* edição;
* exclusão;
* paginação;
* filtros;
* seleção de registro;
* limpeza de estado.

## Store Específica

Cada módulo/tela deve criar sua store específica apenas com regras próprias daquele contexto.

Exemplo:

```txt
CustomerStore
→ BaseCrudStore
→ BaseStore
```

A IA não deve repetir manualmente estados e métodos comuns em cada store.

---

# Reaproveitamento de Componentes e Utilitários

Antes de criar qualquer componente, widget, field, botão, helper ou função utilitária, a IA deve verificar se já existe algo reutilizável no projeto.

## Componentes específicos do módulo

Se o componente for usado apenas dentro de um módulo, criar em:

```txt
src/modules/{module}/presentation/widgets
```

## Componentes compartilhados

Se o componente puder ser usado por vários módulos, criar em:

```txt
src/shared/widgets
```

ou em package compartilhado do monorepo.

Exemplos:

* BaseTextField
* BaseButton
* SearchField
* ConfirmDialog
* MoneyField
* DateField
* CnpjField
* CepField

## Extensions, Formatters e Validators

Formatações e validações reutilizáveis devem ficar em helpers/extensions compartilhadas.

Exemplos:

* validar CNPJ;
* validar CPF;
* validar CEP;
* formatar moeda;
* formatar telefone;
* formatar documento;
* limpar máscara;
* converter string para número;
* normalizar texto.

Nunca codificar manualmente campos repetitivos em cada cadastro quando existir possibilidade de criar componente base reutilizável.

---

# Visão de Produto e Reuso Futuro

Ao criar qualquer módulo, componente, service, provider, store, helper ou fluxo, a IA deve analisar possíveis usos futuros antes de implementar.

A IA deve se perguntar:

* Isso será útil apenas nesta tela?
* Isso pode ser reutilizado em outro módulo?
* Isso pode virar componente compartilhado?
* Isso pode virar package do monorepo?
* Isso pode ser usado em outro produto da Noturno?
* Existe chance de outro app precisar disso no futuro?
* Existe uma forma de criar mais genérica sem complicar demais?

Quando houver chance real de reuso, preparar a estrutura para crescer.

Exemplo:

Um módulo de chat não deve ser criado preso a um único app se puder futuramente atender:

* ERP;
* DoctorCar;
* Sales;
* Delivery;
* Stock/WMS;
* Portal Web;
* App Cliente.

A IA deve equilibrar:

* reuso futuro;
* simplicidade atual;
* manutenção;
* clareza;
* custo de complexidade.

Antes de implementar, a IA deve informar se aquilo será criado como:

* código específico da tela;
* widget específico do módulo;
* shared widget;
* helper compartilhado;
* package reutilizável;
* módulo independente.

E justificar a escolha.

---

# Uso de Pacotes Externos

A preferência do projeto é utilizar recursos nativos e componentes próprios sempre que possível.

A IA não deve instalar pacotes externos sem necessidade real.

Priorizar:

* código próprio;
* componentes internos;
* recursos nativos do React/Next.js/TypeScript;
* shared widgets do projeto;
* helpers internos;
* extensions/utilitários próprios.

Pacotes externos só devem ser considerados quando:

* a funcionalidade for complexa;
* implementar manualmente gerar alto risco;
* houver ganho claro de produtividade;
* o pacote for maduro e amplamente utilizado;
* o pacote tiver manutenção ativa;
* a documentação for boa;
* a comunidade for relevante.

Evitar pacotes:

* muito novos;
* pouco usados;
* sem manutenção recente;
* sem comunidade;
* com documentação fraca;
* que substituem algo simples de criar internamente.

Antes de adicionar qualquer dependência, a IA deve justificar por que o pacote é necessário e por que não vale a pena implementar internamente.

---

# Identidade Visual e Design System

O projeto deve seguir a identidade visual da Noturno Softwares.

A IA não deve criar telas com aparência genérica de template ou visual padrão de IA.

Nunca utilizar cores fora da paleta oficial sem autorização.

## Paleta oficial

```ts
export const NoturnoColors = {
  orange: "#FFB621",
  orangeDark: "#FF9500",

  white: "#FFFFFF",

  greyLight: "#999999",
  greyLightClean: "#D9D9D9",
  greyLightClean2: "#27272A",
  greyLightClean3: "#3F3F47",
  greyDark: "#2E2F36",

  black: "#040404",
  black2: "#1C1C1C",
  black3: "#111820",
  blackSecondary: "#131417",

  red: "#FF2626",
  yellow: "#FFDA0C",

  green: "#00BA81",
  greenLight: "#96D544",

  transparent: "transparent",

  blue: "#036AE3",
  blueDark: "#3FA5EE",
  blueLight: "#21A5EE",
} as const;
```

A IA não deve:

* inventar cores novas;
* usar paletas externas;
* instalar bibliotecas visuais sem autorização;
* criar aparência genérica de template;
* aplicar estilos fora da identidade da Noturno.

---

# Qualidade de Código e Manutenibilidade

O objetivo deste projeto não é apenas funcionar.

O objetivo é produzir software duradouro, desacoplado, testável, compreensível e fácil de manter.

Toda implementação deve considerar:

* legibilidade;
* manutenibilidade;
* escalabilidade;
* reaproveitamento;
* testabilidade;
* baixo acoplamento;
* alta coesão.

A IA nunca deve priorizar apenas "fazer funcionar".

---

# Organização de Arquivos

A IA deve evitar arquivos excessivamente grandes.

Quando uma classe, store, service, repository ou componente crescer excessivamente, deve avaliar separação em unidades menores.

Objetivo:

* facilitar manutenção;
* facilitar navegação;
* facilitar testes;
* reduzir complexidade.

---

# Responsabilidade Única

Cada classe deve possuir responsabilidade clara.

Evitar classes que:

* fazem tudo;
* conhecem tudo;
* dependem de tudo.

Antes de adicionar código a uma classe existente, analisar:

* essa responsabilidade pertence realmente a esta classe?
* existe uma abstração melhor?
* existe um local mais adequado?

---

# Encapsulamento

Tudo que não precisa ser público deve ser privado.

Antes de expor:

* propriedades;
* métodos;
* serviços;
* funções.

A IA deve analisar se realmente existe necessidade de acesso externo.

Priorizar encapsulamento.

---

# Agrupamento por Contexto

O código deve ser organizado por contexto funcional.

Stores, services, repositories e arquivos com múltiplos blocos devem agrupar funções relacionadas por contexto.

Exemplos de agrupamento:

```txt
Load
Save
Delete
Filters
Validation
Mapping
Authentication
Session
Queries
Commands
```

O objetivo é facilitar leitura e navegação.

---

# Documentação de Funções Complexas

Funções simples não precisam de documentação excessiva.

Funções complexas devem possuir documentação explicando:

* objetivo;
* contexto;
* parâmetros;
* retorno;
* regras;
* decisões importantes;
* observações de manutenção.

Antes de concluir uma função complexa, a IA deve avaliar:

* quem nunca viu este código consegue entender?
* quem assumir este projeto daqui 5 anos consegue manter?
* a responsabilidade está clara?
* os nomes estão claros?
* existe uma abstração melhor?
* existe excesso de complexidade?

---

# Boas Práticas Consolidadas

A IA deve priorizar práticas amplamente adotadas e comprovadas por grandes empresas.

Evitar:

* modismos;
* arquiteturas experimentais;
* soluções pouco utilizadas;
* abstrações desnecessárias;
* dependências frágeis.

Priorizar:

* padrões consolidados;
* soluções maduras;
* código previsível;
* código fácil de manter;
* arquitetura duradoura.

---

# Compatibilidade e Evolução Sem Quebra

Toda implementação deve considerar compatibilidade com versões anteriores.

Antes de alterar models, contratos, rotas, stores, providers ou comportamento visual, a IA deve analisar:

* Isso quebra alguma tela existente?
* Isso altera contrato já usado?
* Isso muda comportamento esperado?
* Existe cliente usando fluxo antigo?
* Precisa manter fallback?
* Precisa versionar API/model?
* Precisa criar migração?

Nunca remover ou alterar comportamento existente sem avaliar impacto.

---

# Versionamento de Contratos

Contratos de API devem ser tratados como compromisso entre frontend e backend.

Alterações em JSON de entrada ou saída devem ser documentadas.

Quando houver risco de quebra, preferir criar nova versão:

```txt
v1
v2
```

ou novo mapper/provider mantendo compatibilidade.

---

# Migrações e Depreciações

Quando uma funcionalidade antiga precisar ser substituída, não remover imediatamente.

Preferir:

1. criar nova implementação;
2. manter compatibilidade com a antiga;
3. marcar a antiga como depreciada;
4. documentar plano de remoção;
5. remover somente quando seguro.

---

# Testabilidade

Toda implementação deve considerar testes desde o início.

A arquitetura deve permitir:

* mocks;
* substituição de dependências;
* testes unitários;
* testes de integração.

A IA deve considerar:

* teste de model/mappers;
* teste de application/usecase;
* teste de repository com mock;
* teste de store;
* teste de componente quando aplicável.

Nunca criar código que dependa diretamente de API real para ser testado.

---

# Logs e Diagnóstico

Funcionalidades importantes devem prever diagnóstico.

A IA deve avaliar necessidade de:

* logs técnicos;
* mensagens amigáveis;
* rastreio de erro;
* contexto do erro;
* identificação do fluxo que falhou.

Erros não devem ser silenciosos.

---

# Segurança

A IA deve considerar segurança desde o início.

Nunca expor:

* tokens;
* senhas;
* chaves;
* dados sensíveis;
* regras internas do backend.

Dados sensíveis devem ser tratados com cuidado na UI, logs e documentação.

---

# Performance

Antes de criar listas, filtros, dashboards ou componentes grandes, analisar:

* volume de dados;
* paginação;
* debounce;
* cache;
* renderização desnecessária;
* componentes pequenos;
* memoização quando fizer sentido.

Performance deve ser pensada desde o início, sem exagerar em otimização prematura.

---

# Acessibilidade e Usabilidade

Interfaces devem ser bonitas, mas também claras e usáveis.

Considerar:

* contraste;
* foco por teclado;
* textos claros;
* botões com intenção evidente;
* mensagens de erro compreensíveis;
* responsividade;
* estados vazios;
* loading;
* erro;
* sucesso.

---

# Menu Ajuda e Base de Conhecimento

Todo projeto deve possuir menu de Ajuda ou Base de Conhecimento.

Tudo que for desenvolvido deve atualizar essa base.

Ao implementar uma nova funcionalidade, a IA deve documentar:

* nome da funcionalidade;
* o que ela faz;
* onde acessar;
* rota/tela relacionada;
* como utilizar;
* regras de negócio;
* permissões, se houver;
* impactos no sistema;
* exemplos de uso.

Nenhuma funcionalidade relevante deve ser considerada concluída sem atualização da Base de Conhecimento.

---

# Documentação

Toda funcionalidade relevante deve possuir documentação.

Toda funcionalidade complexa deve possuir especificação em:

```txt
docs/specifications
```

Toda decisão arquitetural deve ser registrada em:

```txt
docs/decisions
```

Toda documentação de módulo deve ser registrada em:

```txt
docs/modules
```

Toda documentação de arquitetura deve ser registrada em:

```txt
docs/architecture
```

---

# Padrão de Especificação

Funcionalidades complexas devem possuir especificação contendo:

* objetivo;
* contexto;
* regras de negócio;
* fluxo;
* entradas;
* saídas;
* casos de erro;
* JSON de entrada;
* JSON de saída;
* mock inicial;
* integração futura;
* impactos no sistema;
* rota/tela relacionada;
* atualização da Base de Conhecimento.

---

# Entendimento Antes da Implementação

A IA não deve implementar funcionalidades quando houver dúvida relevante sobre a regra de negócio.

Em caso de dúvida, deve solicitar esclarecimentos antes de codificar.

A IA não deve assumir comportamento de negócio.

A IA não deve inventar regras.

A IA deve priorizar entendimento correto antes da implementação.

---

# Preservação de Comportamento

Antes de alterar código existente, a IA deve analisar:

* o que já funciona;
* quem depende disso;
* se o comportamento atual será alterado;
* se existe risco de regressão.

Correções e melhorias não devem alterar comportamento sem necessidade.

Sempre priorizar compatibilidade.

---

# Independência da IA

O projeto deve continuar evoluindo mesmo sem acesso à IA.

Portanto:

* código deve ser legível;
* arquitetura deve ser documentada;
* regras devem ser documentadas;
* fluxos devem ser documentados;
* decisões devem ser documentadas.

Nenhuma implementação deve depender do conhecimento exclusivo da IA.

Todo conhecimento relevante deve estar no repositório.

---

# Commits e Histórico

Toda alteração relevante deve ser versionada no Git.

Commits devem ser claros e indicar o objetivo da mudança.

Exemplos:

```txt
feat(customers): add customer block flow
docs(customers): document block customer feature
refactor(auth): isolate login provider
fix(customers): preserve old status mapping
```

---

# Checklist Antes de Implementar

Antes de codificar, a IA deve verificar:

* existe especificação?
* existe contrato JSON?
* existe componente reutilizável?
* existe helper reutilizável?
* existe store base?
* existe risco de quebra?
* precisa documentar base de conhecimento?
* precisa teste?
* precisa versionamento?
* precisa compatibilidade com fluxo antigo?
* precisa analisar singleton/instância?
* precisa analisar reuso futuro?
* precisa package compartilhado?
* precisa evitar pacote externo?

---

# Checklist Antes de Finalizar

Antes de concluir, a IA deve garantir:

* código compilando;
* sem regra de negócio na tela;
* sem API direta na presentation;
* sem cor fora da paleta;
* sem pacote externo injustificado;
* sem duplicação desnecessária;
* documentação atualizada;
* base de conhecimento atualizada;
* testes planejados ou criados;
* impactos informados;
* compatibilidade avaliada;
* código legível e organizado;
* responsabilidades bem separadas;
* manutenção futura considerada.

---

# Desenvolvimento Assistido por IA

Antes de implementar qualquer funcionalidade, a IA deve:

1. analisar a arquitetura existente;
2. respeitar os padrões definidos neste documento;
3. verificar se existe componente, helper, store ou estrutura reutilizável;
4. verificar se existe contrato JSON de entrada e saída;
5. analisar ciclo de vida das instâncias;
6. analisar possibilidade de reuso futuro;
7. informar quais arquivos pretende criar ou alterar;
8. explicar o fluxo da solução;
9. justificar uso de singleton, injeção de dependência ou pacote externo quando aplicável;
10. criar ou atualizar documentação;
11. somente depois realizar a implementação.

A IA não deve criar estruturas arquiteturais diferentes das definidas neste projeto.

---

# Regra Final

A IA deve sempre pensar em longo prazo.

O código deve ser feito para durar anos, não apenas para passar no teste visual imediato.

O objetivo não é apenas gerar código.

O objetivo é criar patrimônio tecnológico documentado, versionado, testável, escalável e sustentável.
