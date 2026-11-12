# AGENTS.md — MyBank

| Campo | Valor |
| --- | --- |
| Identificador | MB-GOV-AGENTS |
| Classe | Documento oficial de governança do MyBank |

## Sumário

- [1. Objetivo do projeto](#1-objetivo-do-projeto)
- [2. Regras fundamentais de trabalho](#2-regras-fundamentais-de-trabalho)
- [3. Princípios de engenharia](#3-principios-de-engenharia)
- [4. Arquitetura e organização](#4-arquitetura-e-organizacao)
- [5. Regras financeiras obrigatórias](#5-regras-financeiras-obrigatorias)
- [6. Regras de negócio](#6-regras-de-negocio)
- [7. Segurança](#7-seguranca)
- [8. Auditoria, privacidade e retenção](#8-auditoria-privacidade-e-retencao)
- [9. Banco de dados e persistência](#9-banco-de-dados-e-persistencia)
- [10. API e contratos](#10-api-e-contratos)
- [11. Testes e qualidade](#11-testes-e-qualidade)
- [12. Ambiente e infraestrutura](#12-ambiente-e-infraestrutura)
- [13. Backup e recuperação](#13-backup-e-recuperacao)
- [14. Logs e observabilidade](#14-logs-e-observabilidade)
- [15. Documentação](#15-documentacao)
- [16. Relatório obrigatório de conclusão](#16-relatorio-obrigatorio-de-conclusao)
- [17. Regra final](#17-regra-final)
- [18. Decisões complementares aprovadas — consolidação documental](#18-decisoes-complementares-aprovadas-consolidacao-documental)

## 1. Objetivo do projeto

O MyBank é um sistema bancário digital fictício, desenvolvido para fins educacionais e de portfólio.

O projeto deve demonstrar boas práticas de engenharia de software, arquitetura, segurança, organização de código, consistência financeira, testes e documentação.

**Não é uma instituição financeira real.** Não realizar integrações bancárias, pagamentos, transferências ou operações financeiras reais.

## 2. Regras fundamentais de trabalho

O agente deve:

- Trabalhar em etapas pequenas, independentes e verificáveis.
- Executar somente a etapa expressamente autorizada pelo usuário.
- Não iniciar etapas futuras automaticamente.
- Não interpretar silêncio, conclusão técnica ou aprovação parcial como autorização.
- Preservar implementações e decisões anteriormente aprovadas.
- Evitar alterações fora do escopo autorizado.
- Comunicar problemas, riscos, limitações e dependências antes de tomar decisões relevantes.
- Solicitar autorização antes de alterar decisões arquiteturais ou regras de negócio aprovadas.

### 2.1. Ciclo obrigatório de cada etapa

1. Verificar o estado atual do projeto e a documentação.
2. Identificar o escopo e os critérios de aceite da etapa.
3. Implementar somente o que foi autorizado.
4. Executar testes e validações pertinentes.
5. Revisar qualidade, segurança, arquitetura e documentação.
6. Apresentar relatório de conclusão.
7. Aguardar decisão explícita do usuário.

O usuário poderá responder:

- **APROVAR:** aceitar a etapa.
- **AJUSTAR:** solicitar correções.
- **REVISAR:** solicitar análise ou esclarecimentos adicionais.

Nenhuma etapa seguinte poderá começar antes da aprovação correspondente.

## 3. Princípios de engenharia

Aplicar consistentemente:

- Clean Code.
- SOLID.
- DRY.
- KISS.
- YAGNI.
- Separação de responsabilidades.
- Alta coesão e baixo acoplamento.
- Legibilidade e manutenibilidade.
- Modularidade sem abstrações desnecessárias.

Evitar:

- Classes e métodos excessivamente grandes.
- Duplicação de regras de negócio.
- Dependências circulares.
- Valores mágicos.
- Código morto e imports desnecessários.
- Tratamento de exceções vazio.
- Regras de negócio em controllers.
- Mistura entre domínio e infraestrutura.
- Abstrações prematuras.
- Otimizações prematuras.
- Dependências sem justificativa técnica.

Priorizar soluções simples, explícitas e sustentáveis.

## 4. Arquitetura e organização

### 4.1. Arquitetura aprovada

- Monólito modular.
- Arquitetura Hexagonal.
- Separação entre domínio, aplicação e infraestrutura.
- Comunicação síncrona para operações que exigem consistência e resposta imediata.
- Eventos assíncronos para notificações e tarefas secundárias.
- Transactional Outbox utilizando PostgreSQL.
- Sem microsserviços, Kafka ou RabbitMQ na primeira versão.

### 4.2. Tecnologias

**Backend**
- Java 25 LTS.
- Spring Boot.
- Maven e Maven Wrapper.
- Spring Security.
- Spring Data JPA.
- Flyway.
- PostgreSQL.
- REST.
- OpenAPI 3.1.1 e Springdoc.

**Frontend**
- React.
- JavaScript.
- Vite.
- React Router.
- Tailwind CSS.
- TanStack Query.
- React Hook Form e Zod.
- Axios.
- Recharts.
- Lucide React.

**Infraestrutura**
- Docker.
- Docker Compose.
- Dev Containers.
- GitHub Actions.

### 4.3. Módulos de negócio

- Identity.
- Customer.
- Account.
- Transaction.
- Ledger.
- Payment.
- Loan.
- Card.
- Notification.
- Admin.
- Audit.
- Privacy.

Cada módulo deve manter suas responsabilidades e evitar dependências indevidas.

Estrutura conceitual:

- `domain`: entidades, objetos de valor e regras de negócio.
- `application`: casos de uso e orquestração.
- `infrastructure`: REST, persistência, segurança e integrações.

Não criar interfaces para todos os componentes. Introduzir abstrações quando houver necessidade arquitetural concreta.

## 5. Regras financeiras obrigatórias

### 5.1. Valores monetários

- Utilizar `BigDecimal` no Java.
- Utilizar `NUMERIC(19,2)` no PostgreSQL.
- Nunca utilizar `float` ou `double` para valores financeiros.
- A moeda suportada será exclusivamente BRL.
- Não implementar conversão de moedas, câmbio ou cotações.

### 5.2. Ledger

O Ledger é a fonte oficial dos saldos.

Toda operação financeira contabilizada deve:

- Utilizar partidas dobradas.
- Garantir igualdade entre débitos e créditos.
- Ser executada em transação ACID.
- Ter proteção contra duplicidade por idempotência.
- Respeitar bloqueios e concorrência.
- Manter lançamentos contabilizados imutáveis.
- Utilizar estorno ou compensação para correções.

A coluna `bank_accounts.balance_projection` é somente uma projeção de consulta rápida. Deve ser atualizada atomicamente com os lançamentos do Ledger e permitir reconciliação.

Operações pendentes não reservam saldo. O saldo e as condições devem ser revalidados durante o processamento.

### 5.3. Restrições financeiras

- Transferências somente entre contas MyBank.
- Depósitos e saques simulados por contas contábeis de compensação.
- Pagamentos limitados a serviços fictícios.
- Compras de cartões são simuladas e debitam o saldo bancário.
- Empréstimos utilizam o sistema Price.
- Comprometimento máximo de renda: 30%.
- Não permitir alteração direta de saldo por administradores.

## 6. Regras de negócio

### 6.1. Clientes e contas

- Cada cliente participa de apenas uma conta ativa.
- A conta pode ser individual ou conjunta.
- Conta individual possui exatamente um titular.
- Conta conjunta possui dois ou mais titulares.
- Titulares ativos possuem direitos iguais.
- Convites devem ser aceitos pelo destinatário autenticado.
- Não permitir remoção unilateral de titulares.
- Encerramento conjunto exige consentimento dos titulares ativos.
- Conta encerrada não pode ser reativada diretamente.
- Preservar o histórico financeiro.

O número de conta deve possuir nove dígitos, incluindo dígito verificador, ser gerado pelo backend e ser único.

### 6.2. Empréstimos

- Permitir múltiplos empréstimos simultâneos, respeitando a capacidade de pagamento.
- Utilizar renda declarada e registrada no momento da solicitação.
- Registrar snapshot das condições aprovadas.
- Aplicar comprometimento máximo de 30%.
- Controlar concorrência na análise e contratação.
- Registrar desembolso no Ledger.
- Manter histórico das parcelas e dos estados.

### 6.3. Cartões

- Cartões exclusivamente virtuais e fictícios.
- Permitir múltiplos cartões por cliente.
- Limite representa capacidade de gasto, não crédito.
- Compras simuladas debitam o saldo da conta.
- Estornos recompõem saldo e limite conforme os lançamentos.
- Não implementar bandeiras ou compras reais.

### 6.4. Estados

Respeitar as máquinas de estados aprovadas para transações, pagamentos, empréstimos, convites e cartões.

Toda transição deve ser validada no domínio e registrada no histórico correspondente.

Não permitir reabertura indevida de estados finais.

## 7. Segurança

### 7.1. Senhas e autenticação

- Hash de senhas com Argon2id.
- Parâmetros iniciais: memória 19 MiB, iterações 2 e paralelismo 1.
- Salt aleatório de 16 bytes.
- Hash de 32 bytes.
- Calibrar desempenho no ambiente de execução.

Autenticação com JWT:

- Access Token com validade inicial de 10 minutos.
- Refresh Token com validade inicial de 7 dias.
- Rotação de Refresh Token.
- Hash de Refresh Tokens persistidos.
- Revogação de sessões.
- Detecção de reutilização de tokens.
- Cookies HttpOnly, Secure e SameSite para Refresh Token.

O Access Token deve permanecer em memória no frontend. Nunca armazenar tokens de autenticação em `localStorage` ou `sessionStorage`.

### 7.2. Segundo fator

- TOTP obrigatório para ADMIN.
- TOTP opcional para CUSTOMER.
- Códigos de recuperação armazenados como hash.
- Segredo TOTP criptografado.
- Proteção contra tentativas excessivas.
- Reautenticação para operações sensíveis.
- Administradores não podem desativar o próprio requisito obrigatório de 2FA.

O mecanismo de código demonstrativo deve permanecer isolado em ambiente de desenvolvimento.

### 7.3. Criptografia

Utilizar AES-256-GCM para segredos que precisem ser recuperados.

- Chave de 256 bits.
- Nonce de 96 bits, único por operação.
- Tag de autenticação de 128 bits.
- Chaves fora do banco de dados.
- Gerenciamento externo de segredos em produção.
- Suporte a versionamento e rotação de chaves.

Senhas devem ser armazenadas como hash, nunca criptografadas de forma reversível.

### 7.4. Autorização

O backend é a autoridade de segurança.

Validar autenticação, autorização, titularidade e escopo em cada operação protegida.

Não confiar em:
- Menus ocultos.
- Rotas protegidas somente no frontend.
- Campos enviados pelo cliente.
- Identificadores sem validação de propriedade.

Aplicar validação de entradas, rate limiting, proteção contra XSS e CSRF conforme a estratégia de autenticação, CSP e respostas de erro sem exposição de detalhes internos.

### 7.5. Administração

- Perfis: CUSTOMER e ADMIN.
- Não criar perfil SUPPORT.
- Todos os administradores possuem as mesmas permissões.
- Operações administrativas críticas exigem justificativa, confirmação adicional e auditoria.
- Não existe fluxo de segunda aprovação.
- Não permitir desativar o último administrador ativo.
- Administradores não podem modificar saldos diretamente.

## 8. Auditoria, privacidade e retenção

Registrar ações administrativas, operações relevantes e consultas de dados sensíveis.

O histórico financeiro, Ledger e auditoria não podem ser apagados por operações administrativas comuns.

Aplicar políticas de retenção, anonimização e eliminação de dados pessoais conforme a LGPD, respeitando finalidade, base legal e obrigações de retenção.

Não utilizar um campo genérico `deleted` indiscriminadamente em todas as tabelas.

## 9. Banco de dados e persistência

- PostgreSQL.
- Identificadores internos UUID.
- Migrações versionadas com Flyway.
- Restrições de integridade no banco sempre que aplicáveis.
- Índices justificados pelos padrões de consulta.
- Transações e bloqueios adequados para operações concorrentes.

Não alterar estrutura persistida sem migração correspondente.

Regras que envolvem múltiplas linhas, como partidas dobradas e soma de percentuais, devem ser garantidas por lógica transacional e mecanismos apropriados, não por CHECK simplificado.

## 10. API e contratos

- REST.
- Prefixo `/api/v1`.
- OpenAPI 3.1.1.
- Documento `docs/05-api/openapi.yaml`.
- Springdoc e Swagger UI.
- DTOs específicos para entrada e saída.
- Validação de dados.
- Tratamento global de exceções.
- Erros padronizados.
- Paginação, filtros e ordenação quando aplicáveis.

Não expor entidades JPA diretamente nos contratos da API.

Mudanças incompatíveis devem ser avaliadas e documentadas antes de serem implementadas.

## 11. Testes e qualidade

### Backend

- JUnit 5.
- Mockito.
- Spring Boot Test.
- Testcontainers.
- ArchUnit.
- JaCoCo.

### Frontend

- Vitest.
- React Testing Library.
- Playwright.

### Requisitos de validação

Testar, conforme o escopo:

- Regras de negócio.
- Casos de uso.
- Integração com persistência.
- Contratos da API.
- Autenticação e autorização.
- Operações financeiras.
- Idempotência.
- Concorrência.
- Transições de estado.
- Segurança.
- Fluxos de interface.
- Responsividade e acessibilidade.

Executar análise estática, testes e verificações pertinentes antes de concluir cada etapa.

Não reduzir cobertura ou remover testes para fazer uma implementação passar sem justificativa e autorização.

## 12. Ambiente e infraestrutura

Antes de iniciar funcionalidades:

- Validar JDK 25.
- Validar Maven Wrapper.
- Validar Node.js LTS.
- Fixar a versão do Node em `.nvmrc`.
- Manter `package-lock.json`.
- Configurar Docker Compose.
- Configurar Dev Containers.
- Criar `.env.example`.
- Validar conexão com PostgreSQL.
- Validar inicialização do backend e frontend.
- Documentar comandos de execução e testes.

Não iniciar implementação funcional enquanto o ambiente não estiver validado e aprovado.

Não instalar ferramentas globalmente sem necessidade justificada.

## 13. Backup e recuperação

Política inicial do projeto:

- Backup completo diário com `pg_dump`.
- Retenção de sete backups diários.
- Retenção de quatro backups semanais.
- Retenção de três backups mensais.
- RPO de até 24 horas.
- RTO de até 4 horas.
- Backups criptografados e armazenados fora do ambiente principal.
- Backup antes de migrações importantes.
- Testes mensais de restauração.

Point-in-Time Recovery não faz parte da primeira versão, podendo ser avaliado futuramente.

## 14. Logs e observabilidade

Utilizar SLF4J e Logback.

Os logs devem ser úteis para diagnóstico e auditoria, sem expor:

- Senhas.
- Tokens.
- Segredos criptográficos.
- Códigos de recuperação.
- Dados pessoais desnecessários.
- Informações financeiras sensíveis sem justificativa.

Registrar falhas relevantes, tentativas de acesso indevido e erros operacionais.

## 15. Documentação

Manter atualizados:

- `AGENTS.md`.
- `ROADMAP.md`.
- ADRs.
- Documentação de arquitetura.
- DER e dicionário de dados.
- Contratos OpenAPI.
- Documentação de instalação e execução.
- Documentação de testes.
- Decisões de segurança e operação.

Toda alteração relevante deve atualizar a documentação correspondente.

Não modificar decisões aprovadas silenciosamente.

## 16. Relatório obrigatório de conclusão

Ao terminar cada etapa, apresentar:

1. Objetivo e escopo executado.
2. Arquivos criados.
3. Arquivos modificados.
4. Testes executados.
5. Resultados das validações.
6. Problemas e limitações encontrados.
7. Critérios de aceite atendidos e pendentes.
8. Próxima etapa sugerida.

Finalizar sempre perguntando explicitamente se o usuário deseja **APROVAR**, **AJUSTAR** ou **REVISAR**.

## 17. Regra final

A prioridade do projeto é construir um sistema coerente, seguro, testável, documentado e sustentável.

O agente deve agir como um desenvolvedor responsável: compreender antes de modificar, justificar decisões, preservar o que funciona, testar o que altera e nunca ultrapassar o escopo autorizado pelo usuário.

## 18. Decisões complementares aprovadas — consolidação documental

- CPF é o único documento de identificação na primeira versão e deve ser protegido com AES-256-GCM e HMAC-SHA-256 independente.
- Chaves criptográficas devem permanecer fora do banco e do código-fonte, com versionamento e suporte à rotação.
- Empréstimos contratados preservam snapshot imutável das condições aprovadas.
- Estornos administrativos são integrais, idempotentes, auditados, sujeitos a limites configurados e geram lançamentos compensatórios sem alterar o Ledger original.
- Reprocessamentos administrativos da Outbox preservam tentativas anteriores e exigem autorização ADMIN, 2FA, reautenticação, justificativa, confirmação, auditoria, idempotência e controle de concorrência.
- O contrato aprovado inclui `POST /api/v1/admin/outbox-events/{eventId}/reprocess`, resposta `202 Accepted` e `statusUrl`.
- `FAILED` é o estado terminal oficial e elegível para reprocessamento da Outbox. `DEAD_LETTER` é somente encaminhamento ou armazenamento operacional, não estado alternativo.
