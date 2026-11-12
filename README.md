# MyBank

**Seu dinheiro. Suas possibilidades.**

> Aplicação educacional de banco digital fictício, desenvolvida como projeto de portfólio. O MyBank não movimenta dinheiro real, não realiza Pix, não acessa instituições financeiras e não processa pagamentos externos.

## Sumário

- [Visão geral](#visao-geral)
- [Estado do projeto](#estado-do-projeto)
- [Funcionalidades especificadas](#funcionalidades-especificadas)
- [Arquitetura e tecnologias](#arquitetura-e-tecnologias)
- [Módulos internos](#modulos-internos)
- [Decisões técnicas](#decisoes-tecnicas)
- [Segurança prevista](#seguranca-prevista)
- [Nível de complexidade e maturidade técnica](#nivel-de-complexidade-e-maturidade-tecnica)
- [Documentação](#documentacao)
- [Execução](#execucao)

## Visão geral

O MyBank simula problemas comuns de um banco digital: cadastro de clientes, contas individuais e conjuntas, movimentações internas, controle contábil, pagamentos fictícios e produtos demonstrativos. O projeto pretende oferecer uma experiência de cliente e administração apoiada por regras de negócio explícitas, histórico auditável e controles de segurança.

Seu objetivo é demonstrar conhecimentos de engenharia de software, arquitetura, desenvolvimento backend e frontend, segurança, persistência, testes e práticas profissionais. A prioridade é mostrar como esses aspectos se relacionam em um sistema financeiro simulado, sem integração ou operação financeira real.

## Estado do projeto

| Área | Estado atual |
|---|---|
| Especificações e decisões técnicas | Em consolidação documental. Consulte o [Roadmap](ROADMAP.md) e o [índice documental](docs/00-governanca/README.md). |
| Organização dos documentos | Entregue como subentrega aceita para continuidade. A consolidação integral ainda aguarda aprovação. |
| Funcionalidades | Especificadas; nenhuma funcionalidade está implementada. |
| DER e dicionário de dados | Os documentos originais aprovados aguardam recuperação. A revisão existente não os substitui. |
| OpenAPI | YAML 3.1.1 disponível; validação estrutural integral ainda pendente. |
| Ambiente de desenvolvimento | Etapa ainda não iniciada. |

As funcionalidades descritas abaixo são escopo planejado, não recursos disponíveis em uma aplicação executável.

## Funcionalidades especificadas

| Módulo | Escopo planejado | Implementação |
|---|---|---|
| Identity | Cadastro de identidade, autenticação, sessões e autorização | Não iniciada |
| Customer | Cadastro e gerenciamento de clientes e dados de perfil | Não iniciada |
| Account | Contas individuais e conjuntas, titulares e convites | Não iniciada |
| Transaction | Transferências internas, depósitos e saques simulados | Não iniciada |
| Ledger | Lançamentos contábeis e consistência dos saldos | Não iniciada |
| Payment | Pagamentos simulados de serviços fictícios e parcelas | Não iniciada |
| Loan | Simulação, análise, contratação e acompanhamento de empréstimos | Não iniciada |
| Card | Cartões virtuais demonstrativos e compras simuladas | Não iniciada |
| Notification | Notificações e tarefas assíncronas internas | Não iniciada |
| Admin | Operações administrativas controladas | Não iniciada |
| Audit | Registro de ações e preservação do histórico | Não iniciada |
| Privacy | Solicitações e controles de privacidade | Não iniciada |

Todos os fluxos são fictícios e internos ao projeto. Depósitos, saques, transferências, compras e pagamentos não se conectam a serviços financeiros reais.

## Arquitetura e tecnologias

A arquitetura definida combina **Arquitetura Hexagonal** com **Monólito Modular**. Os módulos compartilham uma única aplicação, com limites internos de responsabilidade; não são microsserviços independentes. A organização conceitual separa domínio, aplicação e infraestrutura.

Esta é a stack definida para o projeto, não uma declaração de que as ferramentas já estejam configuradas ou que o sistema esteja implementado.

| Área | Tecnologias definidas |
|---|---|
| Backend | Java 25, Spring Boot, Spring Security, Spring Data JPA, Maven e Flyway |
| Frontend | React, JavaScript, Vite e Tailwind CSS |
| Persistência e infraestrutura | PostgreSQL, Docker, Docker Compose e Dev Containers |
| Qualidade | JUnit 5, Mockito, Testcontainers, ArchUnit, JaCoCo, Vitest, React Testing Library e Playwright |
| Integração e automação | API REST, OpenAPI 3.1.1, JWT Access e Refresh Tokens e GitHub Actions |

## Módulos internos

Os módulos representam áreas de negócio dentro do monólito modular:

- **Identity:** credenciais, autenticação, sessões e permissões.
- **Customer:** cadastro, perfil e dados do cliente.
- **Account:** contas, titulares, convites e estados das contas.
- **Transaction:** operações financeiras simuladas e seus fluxos.
- **Ledger:** registros contábeis que servem de fonte oficial para os saldos.
- **Payment:** pagamentos fictícios e liquidação de parcelas.
- **Loan:** propostas, análise, condições contratuais e parcelas.
- **Card:** cartões virtuais e operações simuladas associadas.
- **Notification:** notificações e tarefas secundárias assíncronas.
- **Admin:** ações administrativas sujeitas a autorização e auditoria.
- **Audit:** rastreabilidade de operações e consultas sensíveis.
- **Privacy:** atendimento e controle de solicitações de privacidade.

## Decisões técnicas

- **Arquitetura Hexagonal e Monólito Modular:** separa responsabilidades sem adicionar a complexidade operacional de microsserviços.
- **Domínio, aplicação e infraestrutura:** mantém regras de negócio separadas de interfaces e detalhes de persistência.
- **Partidas dobradas e Ledger:** operações contabilizadas devem equilibrar débitos e créditos; o Ledger é a fonte oficial dos registros financeiros.
- **Precisão monetária:** `BigDecimal` no Java e `NUMERIC(19,2)` no PostgreSQL evitam representação binária aproximada de valores monetários.
- **ACID e concorrência:** transações e controles de concorrência são necessários para preservar a consistência em operações simultâneas.
- **Idempotência:** permite reconhecer repetições de comandos financeiros e evitar duplicidade de efeitos.
- **Transactional Outbox:** registra eventos junto à transação de negócio para permitir processamento assíncrono rastreável.
- **Autenticação e credenciais:** JWT para sessões, Argon2id para hash de senhas e TOTP para segundo fator são decisões previstas de proteção.
- **Auditoria e histórico:** operações relevantes preservam rastreabilidade, em vez de apagar ou alterar registros históricos.
- **Testes em diferentes níveis:** testes unitários, de integração, arquitetura e interface devem verificar regras, contratos e fluxos.

## Segurança prevista

A segurança será aplicada no backend, que deve validar autenticação, autorização, titularidade e escopo em cada operação. Os perfis previstos são `CUSTOMER` e `ADMIN`.

As especificações também preveem proteção de sessões e tokens, TOTP, limitação de tentativas, validação de entradas e auditoria de ações sensíveis. Para o CPF, está definida criptografia AES-256-GCM e HMAC-SHA-256 independente para pesquisa e unicidade; as chaves devem permanecer fora do banco de dados e do código-fonte.

Esses controles são requisitos de projeto. Sua presença nesta documentação não significa que estejam implementados, testados ou certificados, e não constitui uma garantia de segurança.

## Nível de complexidade e maturidade técnica

O escopo arquitetural tem complexidade elevada: reúne desafios de pleno avançado e temas frequentemente associados a trabalho sênior. A implementação pretendida vai além de um CRUD convencional. Isso descreve o escopo, não classifica automaticamente quem desenvolve o projeto.

| Referência | Aspectos que o projeto pretende exercitar | Evidências ainda necessárias |
|---|---|---|
| Júnior | Funcionalidades isoladas, APIs, persistência, testes fundamentais e aplicação de boas práticas | Código funcional, testes básicos e justificativas compreensíveis das escolhas |
| Pleno | Módulos completos, contratos integrados, modelagem, tratamento de erros, concorrência, segurança e testes de integração | Integração correta entre módulos, testes representativos e decisões técnicas sustentadas |
| Sênior | Consistência financeira, resiliência, recuperação, segurança aprofundada, observabilidade, governança e análise de trade-offs | Implementação validada, testes abrangentes, análise de riscos, operação documentada e manutenção sustentável |

O nível profissional efetivamente demonstrado dependerá da qualidade do código, testes, documentação, justificativas técnicas, segurança e capacidade de manutenção. Esta avaliação trata do escopo e dos critérios técnicos; não é certificação profissional.

## Documentação

- [Regras permanentes do projeto](AGENTS.md)
- [Roadmap](ROADMAP.md)
- [Índice da documentação oficial](docs/00-governanca/README.md)
- [Decisões arquiteturais e técnicas](docs/01-decisoes/)
- [Requisitos funcionais](docs/02-requisitos/)
- [Arquitetura e diagramas](docs/03-arquitetura/)
- [Dados e pendências do modelo](docs/04-dados/)
- [Contratos REST e OpenAPI](docs/05-api/)
- [Fluxos de negócio](docs/06-fluxos/)
- [Especificação do frontend](docs/07-frontend/)
- [Parâmetros operacionais](docs/08-operacao/)
- [Auditoria da consolidação](docs/09-auditoria/)
- [Fontes históricas não normativas](docs/99-fontes-originais/)

## Execução

Ainda não há uma aplicação funcional nem um ambiente configurado para executar o MyBank. A etapa de ambiente e a implementação permanecem não iniciadas; por isso, este README não apresenta comandos de instalação ou execução como se estivessem validados.
