# ROADMAP.md — MyBank

| Campo | Valor |
| --- | --- |
| Identificador | MB-GOV-ROADMAP |
| Classe | Documento oficial de governança do MyBank |

## Sumário

- [1. Objetivo](#1-objetivo)
- [2. Estados das etapas](#2-estados-das-etapas)
- [3. Fase I — Planejamento e documentação](#3-fase-i-planejamento-e-documentacao)
- [4. Fase II — Preparação do ambiente](#4-fase-ii-preparacao-do-ambiente)
- [5. Fase III — Estrutura inicial do projeto](#5-fase-iii-estrutura-inicial-do-projeto)
- [6. Fase IV — Desenvolvimento funcional](#6-fase-iv-desenvolvimento-funcional)
- [7. Fase V — Frontend](#7-fase-v-frontend)
- [8. Fase VI — Qualidade e entrega](#8-fase-vi-qualidade-e-entrega)
- [9. Regras de execução](#9-regras-de-execucao)
- [10. Situação geral](#10-situacao-geral)

## 1. Objetivo

Organizar o desenvolvimento do MyBank em etapas pequenas, sequenciais e verificáveis.

Cada etapa deve possuir escopo definido, entregáveis, dependências e critérios de aceite.

Nenhuma etapa poderá começar sem autorização explícita do usuário.

## 2. Estados das etapas

- NÃO INICIADA
- EM PLANEJAMENTO
- EM EXECUÇÃO
- AGUARDANDO APROVAÇÃO
- APROVADA
- NECESSITA AJUSTES
- BLOQUEADA

### 2.1. Interpretação dos estados e dos artefatos

- **Documento produzido:** o arquivo existe; isso, por si só, não comprova revisão ou aprovação.
- **Documento revisado:** seu conteúdo passou por revisão registrada; isso não aprova automaticamente decisões nem conclui uma etapa.
- **Decisão aprovada:** existe aprovação explícita do usuário ou registro formal de aprovação aplicável, como o estado de uma ADR individual.
- **Etapa formalmente concluída:** os critérios de aceite da etapa foram atendidos e a conclusão recebeu aprovação explícita do usuário.

O estado de uma etapa registra sua execução formal no Roadmap e não é inferido pela existência de documentos ou decisões relacionados. Assim, uma etapa marcada como **NÃO INICIADA** significa que não há registro de início formal daquela etapa; não significa necessariamente que nenhum artefato relacionado exista. A existência desses artefatos também não autoriza alterar o estado para **APROVADA** ou **AGUARDANDO APROVAÇÃO** sem o registro correspondente.

## 3. Fase I — Planejamento e documentação

### Etapa 1 — Regras permanentes do projeto
**Estado:** APROVADA

Entregável:
- AGENTS.md

Critérios de aceite:
- Regras de desenvolvimento definidas.
- Arquitetura e tecnologias registradas.
- Regras financeiras e de segurança documentadas.
- Processo de execução e aprovação estabelecido.

### Etapa 2 — Roadmap do projeto
**Estado:** EM PLANEJAMENTO

Entregável:
- ROADMAP.md

Critérios de aceite:
- Etapas organizadas.
- Dependências identificadas.
- Entregáveis definidos.
- Critérios de aceite documentados.
- Fluxo de aprovação estabelecido.

### Etapa 3 — Registros de decisões arquiteturais
**Estado:** NÃO INICIADA

Entregáveis:
- ADRs individuais.
- Índice de decisões arquiteturais.

Conteúdo:
- Arquitetura Hexagonal e Monólito Modular.
- Tecnologias e versões.
- Organização modular.
- Persistência e banco de dados.
- Ledger e consistência financeira.
- Autenticação e autorização.
- Criptografia e gerenciamento de segredos.
- Comunicação síncrona e assíncrona.
- Estratégia de testes.
- Backup e recuperação.
- Contratos e versionamento da API.

Critérios de aceite:
- Decisões aprovadas registradas.
- Contexto e justificativas documentados.
- Consequências e alternativas consideradas.
- Ausência de contradições com requisitos aprovados.

### Etapa 4 — Especificação funcional consolidada
**Estado:** NÃO INICIADA

Entregáveis:
- Documento de requisitos funcionais.
- Documento de requisitos não funcionais.
- Regras de negócio consolidadas.
- Matriz de permissões.
- Fluxos dos usuários CUSTOMER e ADMIN.
- Máquinas de estados.

Critérios de aceite:
- Todos os módulos descritos.
- Regras de negócio rastreáveis.
- Permissões e restrições documentadas.
- Fluxos financeiros consistentes.
- Casos excepcionais identificados.

### Etapa 5 — Arquitetura e diagramas
**Estado:** NÃO INICIADA

Entregáveis:
- Visão geral da arquitetura.
- Diagrama de módulos.
- Diagrama de componentes.
- Diagramas de sequência dos fluxos críticos.
- Diagramas de implantação.
- Diagramas de segurança e autenticação.

Critérios de aceite:
- Arquitetura compatível com os ADRs.
- Responsabilidades dos módulos identificadas.
- Fluxos síncronos e assíncronos representados.
- Consistência financeira demonstrada nos diagramas.

### Etapa 6 — DER e dicionário de dados
**Estado:** APROVADA — documentação lógica v1.1

Entregável:
- Documento único contendo DER e dicionário de dados.

Conteúdo:
- Entidades e relacionamentos.
- Atributos e tipos.
- Chaves primárias e estrangeiras.
- Restrições e índices.
- Estados e históricos.
- Regras de integridade.
- Estrutura do Ledger.
- Estratégia de persistência e projeções.

Critérios de aceite:
- Modelo compatível com os requisitos.
- Ausência de entidades ou atributos sem justificativa.
- Regras financeiras preservadas.
- Integridade referencial definida.
- Compatibilidade com PostgreSQL e Flyway.

A aprovação cobre o modelo lógico e a documentação v1.1. Permanecem para etapas futuras, sem autorização de implementação nesta aprovação, a estrutura física de `outbox_event_attempts` e os detalhes operacionais da reconciliação de `attempt_count`.

### Etapa 7 — Contratos da API
**Estado:** NÃO INICIADA

Entregáveis:
- Especificação OpenAPI 3.1.1.
- Arquivo `docs/05-api/openapi.yaml`.
- Catálogo de endpoints.
- DTOs de entrada e saída.
- Padrões de erros.
- Regras de paginação, filtros e ordenação.

Critérios de aceite:
- Endpoints organizados por módulo.
- Autenticação e autorização especificadas.
- Validações documentadas.
- Contratos compatíveis com as regras de negócio.
- Operações financeiras com idempotência definida.

### Etapa 8 — Segurança e privacidade
**Estado:** NÃO INICIADA

Entregáveis:
- Modelo de ameaças.
- Matriz de autorização.
- Política de autenticação.
- Fluxos de 2FA.
- Estratégia de proteção de sessões.
- Política de auditoria.
- Política de retenção e privacidade.
- Casos de teste de segurança.

Critérios de aceite:
- Ameaças relevantes identificadas.
- Controles associados às ameaças.
- Permissões verificáveis.
- Proteção de dados documentada.
- Requisitos de auditoria definidos.

### Etapa 9 — Consolidação e revisão geral
**Estado:** AGUARDANDO APROVAÇÃO

Entregáveis:
- Documentação consolidada.
- Índice de documentos.
- Matriz de rastreabilidade.
- Lista de pendências e riscos.
- Versão de referência dos requisitos.
- Relatório de auditoria documental cruzada.
- Especificação OpenAPI YAML materializada; validação estrutural concluída com Redocly CLI 2.73.0: 0 erros, 50 avisos e 0 ignorados. A revisão dos avisos e das pendências semânticas/documentais permanece em aberto.

Critérios de aceite:
- Documentos sem contradições.
- Decisões aprovadas refletidas corretamente.
- Requisitos rastreáveis até arquitetura, dados e API.
- Pendências identificadas.
- Aprovação geral do planejamento.

A consolidação documental desta etapa aguarda aprovação explícita do usuário. A existência de documentos consolidados não autoriza a Etapa 10 nem implementação funcional. Pendências da auditoria estão registradas em `docs/09-auditoria/relatorio-de-consolidacao.md`.

A documentação lógica do DER e do Dicionário de Dados v1.1 foi aprovada. Essa aprovação parcial não altera o estado geral da Etapa 9, que continua aguardando aprovação, nem autoriza a Etapa 10. As pendências físicas de `outbox_event_attempts` e operacionais da reconciliação permanecem registradas para etapas futuras.

A subetapa documental 2.1 — complementação das respostas HTTP `401` e revisão dos componentes OpenAPI — foi aprovada pelo usuário em 2026-11-18. A aprovação limita-se ao escopo dessa subetapa; a validação integral atual com Redocly permanece pendente. O estado geral da Etapa 9 continua aguardando aprovação, e a Etapa 3 do Roadmap (Registros de decisões arquiteturais) permanece não iniciada.

A subetapa documental 3 — revisão dos avisos restantes da OpenAPI — foi aprovada pelo usuário em 2026-11-19. A aprovação abrangeu as descrições das 15 tags, a simplificação do `allOf` e a remoção de `info.license`, pois não há licença formal aprovada. A validação integral com Redocly CLI permanece pendente; esta aprovação não declara a OpenAPI integralmente validada. O estado geral da Etapa 9 continua aguardando aprovação. A Etapa 3 do Roadmap (Registros de decisões arquiteturais) e a Etapa 4 (Especificação funcional consolidada) permanecem não iniciadas.

## 4. Fase II — Preparação do ambiente

### Etapa 10 — Validação de ferramentas e ambiente
**Estado:** NÃO INICIADA

Escopo:
- JDK 25 LTS.
- Maven Wrapper.
- Node.js LTS.
- npm.
- Docker e Docker Compose.
- Dev Containers.
- PostgreSQL.
- Variáveis de ambiente.
- `.env.example`.
- `.nvmrc`.
- Compatibilidade e vulnerabilidades das dependências.

Critérios de aceite:
- Ferramentas instaladas e verificadas.
- Backend e frontend inicializados.
- PostgreSQL acessível.
- Containers funcionais.
- Comandos documentados.
- Ambiente reproduzível.

Não iniciar funcionalidades antes da validação e aprovação desta etapa.

## 5. Fase III — Estrutura inicial do projeto

### Etapa 11 — Estrutura base do repositório
**Estado:** NÃO INICIADA

Escopo:
- Estrutura backend.
- Estrutura frontend.
- Configurações iniciais.
- Organização dos módulos.
- Configuração de qualidade.
- Pipeline inicial de CI.
- Documentação de execução.

Critérios de aceite:
- Estrutura compatível com a arquitetura.
- Compilação e inicialização verificadas.
- Pipeline executando verificações básicas.
- Nenhuma regra de negócio implementada sem autorização.

## 6. Fase IV — Desenvolvimento funcional

As etapas abaixo representam a sequência planejada. A divisão interna e as dependências serão detalhadas antes de cada execução.

### Etapa 12 — Fundamentos de persistência
- PostgreSQL.
- Flyway.
- Estrutura inicial de persistência.
- Restrições e índices.
- Testes de integração.

### Etapa 13 — Identidade e autenticação
- Cadastro.
- Login.
- Argon2id.
- JWT.
- Refresh Token.
- Sessões.
- Recuperação de senha.
- Verificação de e-mail.
- 2FA.

### Etapa 14 — Clientes e perfis
- Dados cadastrais.
- Perfil financeiro.
- Renda declarada.
- Atualização de informações.
- Privacidade.

### Etapa 15 — Contas bancárias
- Criação de contas.
- Numeração e dígito verificador.
- Titulares individuais e conjuntos.
- Convites.
- Participações percentuais.
- Bloqueio e encerramento.

### Etapa 16 — Ledger e infraestrutura financeira
- Plano de contas contábeis.
- Partidas dobradas.
- Lançamentos.
- Projeção de saldo.
- Idempotência.
- Concorrência.
- Estornos e compensações.
- Reconciliação.

### Etapa 17 — Transações financeiras
- Transferências internas.
- Depósitos simulados.
- Saques simulados.
- Histórico.
- Comprovantes.

### Etapa 18 — Pagamentos
- Serviços fictícios.
- Pagamentos imediatos.
- Agendamentos.
- Cancelamentos permitidos.
- Histórico e comprovantes.

### Etapa 19 — Empréstimos
- Solicitação.
- Análise de capacidade.
- Aprovação administrativa.
- Contratação.
- Sistema Price.
- Parcelas.
- Desembolso.
- Quitação e inadimplência simulada.

### Etapa 20 — Cartões virtuais
- Emissão fictícia.
- Limites de gasto.
- Bloqueio e cancelamento.
- Compras simuladas.
- Estornos.

### Etapa 21 — Notificações e eventos
- Transactional Outbox.
- Processamento de eventos.
- Notificações internas.
- E-mail simulado.
- Push no navegador.
- Preferências e histórico.

### Etapa 22 — Administração e auditoria
- Dashboard administrativo.
- Clientes e contas.
- Operações financeiras.
- Gestão de administradores.
- Configurações.
- Auditoria e justificativas.

### Etapa 23 — Privacidade e retenção
- Solicitações de titulares.
- Políticas de retenção.
- Anonimização.
- Eliminação permitida.
- Auditoria das operações de privacidade.

## 7. Fase V — Frontend

### Etapa 24 — Design System
- Identidade visual MyBank.
- Tema escuro.
- Paleta.
- Tipografia.
- Espaçamentos.
- Componentes reutilizáveis.
- Responsividade e acessibilidade.

### Etapa 25 — Área pública
- Home.
- Sobre.
- Produtos.
- Segurança.
- Login.
- Cadastro.
- Recuperação de senha.

### Etapa 26 — Área do cliente
- Dashboard.
- Conta.
- Extrato.
- Transferências.
- Depósitos.
- Saques.
- Pagamentos.
- Empréstimos.
- Cartões.
- Notificações.
- Perfil e segurança.

### Etapa 27 — Área administrativa
- Dashboard.
- Clientes.
- Contas.
- Transações.
- Ledger.
- Empréstimos.
- Pagamentos.
- Usuários e permissões.
- Auditoria.
- Configurações.

O desenvolvimento do frontend deve respeitar os contratos da API e nunca assumir autoridade sobre regras financeiras ou permissões.

## 8. Fase VI — Qualidade e entrega

### Etapa 28 — Testes integrados e E2E
- Fluxos completos de autenticação.
- Fluxos financeiros.
- Conta conjunta.
- Empréstimos.
- Cartões.
- Administração.
- Notificações.
- Segurança.
- Responsividade e acessibilidade.

### Etapa 29 — Segurança e revisão técnica
- Análise estática.
- Auditoria de dependências.
- Testes de autorização.
- Testes de concorrência.
- Testes de idempotência.
- Revisão de exposição de dados.
- Revisão de arquitetura.

### Etapa 30 — Backup e recuperação
- Rotina de backup.
- Criptografia.
- Retenção.
- Teste de restauração.
- Verificação de RPO e RTO.

### Etapa 31 — Documentação e entrega final
- Documentação técnica.
- Manual de execução.
- Guia de testes.
- Guia de demonstração.
- Revisão do repositório.
- Preparação para apresentação de portfólio.

Critérios de aceite:
- Testes e validações concluídos.
- Documentação atualizada.
- Ambiente reproduzível.
- Ausência de pendências críticas conhecidas.
- Aprovação final do usuário.

## 9. Regras de execução

1. Executar uma etapa por vez.
2. Não antecipar etapas sem autorização.
3. Respeitar dependências.
4. Não alterar escopo aprovado silenciosamente.
5. Registrar novas decisões em ADRs quando necessário.
6. Atualizar este roadmap após cada etapa aprovada.
7. Apresentar relatório de conclusão.
8. Aguardar aprovação antes de continuar.

## 10. Situação geral

- Fase I: em consolidação documental.
- Fase II: aguardando conclusão e aprovação da documentação.
- Fase III: não iniciada.
- Fase IV: não iniciada.
- Fase V: não iniciada.
- Fase VI: não iniciada.

**Regra principal:** planejamento aprovado não significa autorização automática para implementação. Toda execução depende de autorização específica do usuário.
