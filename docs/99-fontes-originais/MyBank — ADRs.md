# ADRs — MyBank
## Registro de Decisões Arquiteturais

**Projeto:** MyBank  
**Status geral:** Proposto para consolidação documental  
**Formato:** Architecture Decision Records (ADR)

---

## Índice

| ADR | Decisão |
|---|---|
| ADR-001 | Arquitetura Hexagonal e Monólito Modular |
| ADR-002 | Tecnologias e plataforma de desenvolvimento |
| ADR-003 | Organização dos módulos |
| ADR-004 | PostgreSQL, persistência e migrações |
| ADR-005 | Representação e precisão monetária |
| ADR-006 | Ledger e consistência financeira |
| ADR-007 | Comunicação síncrona, eventos e Outbox |
| ADR-008 | Autenticação e gerenciamento de sessões |
| ADR-009 | Hash de senhas e criptografia de segredos |
| ADR-010 | Autorização e administração |
| ADR-011 | Contas individuais e conjuntas |
| ADR-012 | Empréstimos e cartões virtuais |
| ADR-013 | API REST e contratos OpenAPI |
| ADR-014 | Testes e garantia de qualidade |
| ADR-015 | Backup e recuperação |
| ADR-016 | Privacidade, retenção e auditoria |
| ADR-017 | Estratégia de frontend |

---

## ADR-001 — Arquitetura Hexagonal e Monólito Modular

**Status:** ACEITO

### Contexto

O MyBank precisa apresentar uma arquitetura organizada, modular, testável e sustentável, sem introduzir complexidade operacional desnecessária.

### Decisão

Adotar:
- Arquitetura Hexagonal.
- Monólito Modular.
- Separação entre domínio, aplicação e infraestrutura.
- Dependências direcionadas para o domínio.
- Comunicação entre módulos por contratos bem definidos.

Não utilizar microsserviços na primeira versão.

### Alternativas consideradas

- Monólito tradicional sem separação modular.
- Arquitetura de microsserviços.
- Arquitetura em camadas convencional.

### Consequências

**Positivas:**
- Menor complexidade de infraestrutura.
- Facilidade de desenvolvimento e depuração.
- Domínio independente de tecnologias externas.
- Testabilidade.
- Possibilidade de evolução futura.

**Negativas:**
- Exige disciplina para manter os limites dos módulos.
- O isolamento operacional entre módulos não é equivalente ao de microsserviços.

---

## ADR-002 — Tecnologias e plataforma de desenvolvimento

**Status:** ACEITO

### Contexto

É necessário padronizar as tecnologias e versões para garantir reprodutibilidade e compatibilidade.

### Decisão

**Backend:**
- Java 25 LTS.
- Spring Boot.
- Maven.
- Maven Wrapper.

**Frontend:**
- React.
- JavaScript.
- Vite.
- Node.js LTS.
- npm.

**Infraestrutura:**
- PostgreSQL.
- Docker.
- Docker Compose.
- Dev Containers.
- GitHub Actions.

As versões das ferramentas e dependências deverão ser fixadas e verificadas quanto à compatibilidade.

### Consequências

- Ambiente de desenvolvimento padronizado.
- Menor dependência de configurações locais.
- Facilidade de reprodução dos testes.
- Necessidade de acompanhar compatibilidade entre versões do ecossistema.

---

## ADR-003 — Organização dos módulos

**Status:** ACEITO

### Contexto

O sistema possui diferentes domínios de negócio que precisam permanecer organizados e independentes.

### Decisão

Organizar o backend nos seguintes módulos:

1. Identity.
2. Customer.
3. Account.
4. Transaction.
5. Ledger.
6. Payment.
7. Loan.
8. Card.
9. Notification.
10. Admin.
11. Audit.
12. Privacy.

Cada módulo terá responsabilidades próprias e organização interna baseada em:

- `domain`
- `application`
- `infrastructure`

Um módulo não deve acessar diretamente detalhes internos de outro módulo.

O compartilhamento de conceitos será realizado por contratos explícitos. Um pacote `shared` somente será criado para conceitos genuinamente compartilhados.

### Consequências

- Melhor separação de responsabilidades.
- Redução de dependências indevidas.
- Facilidade de manutenção e testes.
- Necessidade de controlar dependências entre módulos.

---

## ADR-004 — PostgreSQL, persistência e migrações

**Status:** ACEITO

### Contexto

O sistema precisa de persistência relacional confiável, integridade referencial e suporte a transações financeiras.

### Decisão

Adotar:
- PostgreSQL.
- Spring Data JPA.
- Flyway para migrações.
- UUID como identificador interno.
- Restrições de integridade no banco.
- Índices conforme necessidades de consulta.

Toda alteração estrutural deverá possuir migração versionada.

Não realizar alterações manuais não documentadas no esquema do banco.

### Consequências

- Integridade dos dados.
- Histórico controlado de alterações.
- Reprodutibilidade dos ambientes.
- Necessidade de manter compatibilidade entre migrações e versões da aplicação.

---

## ADR-005 — Representação e precisão monetária

**Status:** ACEITO

### Contexto

Operações financeiras exigem precisão decimal e não podem sofrer erros de arredondamento binário.

### Decisão

- Utilizar `BigDecimal` no Java.
- Utilizar `NUMERIC(19,2)` no PostgreSQL.
- Utilizar exclusivamente BRL.
- Não utilizar `float` ou `double` em cálculos financeiros.
- Definir regras explícitas de arredondamento.
- Não implementar câmbio ou conversão de moedas.

### Consequências

- Precisão decimal adequada.
- Padronização de valores financeiros.
- Necessidade de aplicar regras consistentes de escala e arredondamento.

---

## ADR-006 — Ledger e consistência financeira

**Status:** ACEITO

### Contexto

O sistema precisa manter consistência contábil, rastreabilidade e proteção contra duplicidade e concorrência.

### Decisão

Adotar Ledger baseado em partidas dobradas.

Regras:

1. Débitos devem ser iguais aos créditos em cada operação contabilizada.
2. Lançamentos contabilizados são imutáveis.
3. Correções devem ocorrer por estorno ou compensação.
4. Operações financeiras devem ser executadas em transações ACID.
5. Operações devem possuir proteção por idempotência.
6. Contas devem ser bloqueadas de forma determinística quando necessário.
7. O saldo oficial deve ser derivado dos lançamentos do Ledger.

A tabela `bank_accounts` terá:

`balance_projection NUMERIC(19,2) NOT NULL DEFAULT 0.00`

Essa coluna será uma projeção de consulta rápida, atualizada atomicamente junto com os lançamentos financeiros.

Não será considerada fonte oficial do saldo.

Operações pendentes não reservam saldo. O saldo será revalidado durante o processamento.

### Alternativas consideradas

- Manter somente um saldo diretamente na conta.
- Utilizar Ledger sem projeção.
- Utilizar projeção como fonte oficial.

### Consequências

**Positivas:**
- Rastreabilidade financeira.
- Reconciliação.
- Histórico confiável.
- Correções sem apagar lançamentos.
- Maior segurança em operações concorrentes.

**Negativas:**
- Maior complexidade de implementação.
- Necessidade de testes específicos de concorrência e consistência.

---

## ADR-007 — Comunicação síncrona, eventos e Outbox

**Status:** ACEITO

### Contexto

O sistema possui operações que exigem consistência imediata e outras que podem ser processadas posteriormente.

### Decisão

Utilizar comunicação síncrona para:
- Transferências.
- Depósitos e saques.
- Pagamentos durante seu processamento.
- Contratação e desembolso de empréstimos.
- Operações que exigem resposta imediata e consistência financeira.

Utilizar eventos assíncronos para:
- Notificações.
- E-mails simulados.
- Push.
- Tarefas secundárias.

Adotar Transactional Outbox no PostgreSQL.

Não utilizar Kafka ou RabbitMQ na primeira versão.

Os consumidores deverão implementar:
- Idempotência.
- Identificação única de eventos.
- Tentativas limitadas.
- Registro de falhas.
- Prevenção de processamento duplicado.

### Consequências

- Consistência das operações financeiras.
- Menor complexidade operacional.
- Processamento desacoplado de notificações.
- Necessidade de monitorar eventos pendentes e falhos.

---

## ADR-008 — Autenticação e gerenciamento de sessões

**Status:** ACEITO

### Contexto

O sistema precisa proteger sessões, permitir revogação e reduzir os riscos associados ao roubo ou reutilização de tokens.

### Decisão

Adotar Spring Security e JWT.

**Access Token:**
- Validade inicial de 10 minutos.
- Permanência em memória no frontend.
- Payload mínimo.
- Assinatura e validação de issuer, audience e identificador de token (`jti`).

**Refresh Token:**
- Validade inicial de 7 dias.
- Rotação a cada renovação.
- Persistência somente do hash.
- Cookie HttpOnly, Secure e SameSite.
- Revogação no logout.
- Detecção de reutilização.
- Gerenciamento de sessões revogáveis.

Não armazenar tokens em `localStorage` ou `sessionStorage`.

### Segundo fator

- CUSTOMER: opcional.
- ADMIN: obrigatório.
- TOTP como mecanismo principal.
- Códigos de recuperação de uso único.
- Segredos TOTP criptografados.
- Reautenticação para operações sensíveis.

O mecanismo demonstrativo de códigos aleatórios de seis dígitos será restrito ao ambiente de desenvolvimento.

### Consequências

- Melhor controle de sessões.
- Redução da exposição de credenciais.
- Maior segurança para operações sensíveis.
- Necessidade de implementar rotação, revogação e proteção contra reutilização corretamente.

---

## ADR-009 — Hash de senhas e criptografia de segredos

**Status:** ACEITO

### Contexto

O sistema armazena credenciais e segredos que exigem proteção adequada.

### Decisão

**Senhas: Argon2id**

Parâmetros iniciais:
- Memória: 19 MiB.
- Iterações: 2.
- Paralelismo: 1.
- Salt aleatório: 16 bytes.
- Hash: 32 bytes.

Os parâmetros deverão ser calibrados por testes de desempenho no ambiente.

**Segredos recuperáveis: AES-256-GCM**

Parâmetros:
- Chave: 256 bits.
- Nonce: 96 bits, único por operação.
- Tag: 128 bits.

As chaves não devem ser armazenadas no PostgreSQL.

Em produção, utilizar gerenciamento externo de segredos ou KMS. No ambiente local, utilizar configuração externa protegida.

Implementar versionamento e rotação de chaves.

### Consequências

- Proteção adequada de credenciais.
- Proteção de segredos que precisam ser recuperados.
- Necessidade de administrar chaves e planejar sua rotação.
- Senhas não podem ser recuperadas por descriptografia.

---

## ADR-010 — Autorização e administração

**Status:** ACEITO

### Contexto

O sistema precisa separar as permissões dos clientes e administradores, protegendo operações e informações sensíveis.

### Decisão

Utilizar exclusivamente os perfis:

- CUSTOMER.
- ADMIN.

Todos os administradores possuem as mesmas permissões.

Não criar perfil SUPPORT.

A autorização deverá ser validada no backend em cada operação.

Operações administrativas críticas exigem:
- Confirmação adicional.
- Justificativa obrigatória.
- Registro de auditoria.

Não haverá fluxo de segunda aprovação.

Administradores poderão criar, desativar e reativar outros administradores, respeitando a regra de não desativar o último administrador ativo.

O administrador não poderá alterar saldos diretamente.

### Consequências

- Modelo de autorização simples.
- Responsabilidades explícitas.
- Rastreabilidade das ações administrativas.
- Maior importância dos controles de auditoria.

---

## ADR-011 — Contas individuais e conjuntas

**Status:** ACEITO

### Contexto

O MyBank deve suportar contas individuais e conjuntas sem introduzir regras ambíguas de titularidade.

### Decisão

- Cada cliente pode participar de apenas uma conta ativa.
- Conta individual possui exatamente um titular.
- Conta conjunta possui dois ou mais titulares.
- Titulares ativos possuem direitos iguais.
- Convites devem ser aceitos pelo destinatário autenticado.
- Não permitir remoção unilateral de titulares.
- Encerramento conjunto exige consentimento de todos os titulares ativos.
- Saída voluntária respeita o mínimo de titulares.
- Participações percentuais são opcionais.
- Quando definidas, as participações devem totalizar 100%.
- Sem percentuais definidos, o patrimônio permanece compartilhado, sem atribuição individual.

O número da conta será numérico, terá nove dígitos com dígito verificador e será gerado automaticamente pelo backend.

### Consequências

- Regras de titularidade explícitas.
- Necessidade de controlar convites e concorrência.
- Necessidade de validar percentuais em operações transacionais.

---

## ADR-012 — Empréstimos e cartões virtuais

**Status:** ACEITO

### Contexto

O sistema precisa demonstrar produtos financeiros sem realizar operações bancárias reais.

### Decisão

**Empréstimos:**
- Sistema de amortização Price.
- Juros mensais configuráveis.
- Parâmetros e condições registrados no contrato.
- Múltiplos empréstimos simultâneos.
- Comprometimento máximo de 30% da renda declarada.
- Análise e aprovação manual com justificativa e auditoria.
- Desembolso registrado no Ledger.
- Parcelas com histórico de estados.
- Obrigações financeiras preservadas mesmo após encerramento da conta.

**Cartões:**
- Exclusivamente virtuais e fictícios.
- Múltiplos cartões por cliente.
- Limite representa capacidade de gasto, não crédito.
- Compras simuladas debitam o saldo bancário.
- Estornos recompõem saldo e limite.
- Não haverá bandeira, cartão físico ou compra real.

### Consequências

- Demonstração de produtos financeiros.
- Regras de cálculo e controle de capacidade de pagamento.
- Necessidade de conciliar operações de cartão com Ledger e conta bancária.

---

## ADR-013 — API REST e contratos OpenAPI

**Status:** ACEITO

### Contexto

Backend e frontend precisam compartilhar contratos explícitos e documentados.

### Decisão

Adotar:
- API REST.
- Prefixo `/api/v1`.
- OpenAPI 3.1.1.
- Arquivo `openapi.yaml`.
- Springdoc.
- Swagger UI.
- DTOs específicos.
- Validação de entradas.
- Tratamento global de exceções.
- Formato padronizado de erros.
- Paginação, filtros e ordenação quando aplicáveis.

A versão da API será independente da versão da especificação OpenAPI.

### Consequências

- Contratos claros entre frontend e backend.
- Documentação consultável.
- Maior facilidade para testes de integração.
- Necessidade de manter documentação sincronizada com a implementação.

---

## ADR-014 — Testes e garantia de qualidade

**Status:** ACEITO

### Contexto

O projeto exige confiabilidade, segurança e evidências de que suas regras estão funcionando corretamente.

### Decisão

**Backend:**
- JUnit 5.
- Mockito.
- Spring Boot Test.
- Testcontainers.
- ArchUnit.
- JaCoCo.

**Frontend:**
- Vitest.
- React Testing Library.
- Playwright.

Executar testes unitários, de integração, arquiteturais e E2E conforme a natureza da funcionalidade.

Cobrir especialmente:
- Ledger e partidas dobradas.
- Idempotência.
- Concorrência.
- Autorização.
- Titularidade de contas.
- Máquinas de estados.
- Autenticação.
- Segurança.
- Fluxos financeiros.

### Consequências

- Maior confiança nas alterações.
- Detecção antecipada de regressões.
- Necessidade de manter testes atualizados e ambientes reproduzíveis.

---

## ADR-015 — Backup e recuperação

**Status:** ACEITO

### Contexto

O projeto precisa demonstrar uma política documentada de proteção e recuperação de dados.

### Decisão

Política inicial:

- Backup completo diário utilizando `pg_dump`.
- Sete backups diários.
- Quatro backups semanais.
- Três backups mensais.
- RPO de até 24 horas.
- RTO de até 4 horas.
- Backups criptografados.
- Armazenamento fora do ambiente principal.
- Backup antes de migrações importantes.
- Testes mensais de restauração.

Não implementar Point-in-Time Recovery na primeira versão.

### Consequências

- Política simples de executar.
- Recuperação documentada.
- Necessidade de testar restauração periodicamente.
- Perda potencial de alterações entre o último backup e uma falha, limitada pelo RPO definido.

Esta política é dimensionada para o projeto demonstrativo e não representa uma recomendação operacional para um banco real.

---

## ADR-016 — Privacidade, retenção e auditoria

**Status:** ACEITO

### Contexto

O sistema precisa preservar histórico financeiro e, simultaneamente, respeitar direitos dos titulares de dados pessoais.

### Decisão

- Aplicar princípios da LGPD.
- Definir finalidade e base legal para tratamento.
- Registrar ações administrativas e consultas sensíveis.
- Preservar Ledger, transações e auditoria.
- Permitir anonimização ou eliminação de dados pessoais quando legalmente permitido.
- Manter políticas de retenção.
- Não permitir exclusão administrativa comum de histórico financeiro.
- Corrigir operações por estorno ou compensação.

Não criar um campo genérico `deleted` em todas as tabelas.

### Consequências

- Preservação de rastreabilidade.
- Tratamento controlado de dados pessoais.
- Necessidade de políticas específicas por categoria de informação.

---

## ADR-017 — Estratégia de frontend

**Status:** ACEITO

### Contexto

O sistema precisa oferecer uma interface moderna, responsiva, acessível e consistente com a identidade visual MyBank.

### Decisão

Adotar:
- React.
- JavaScript.
- Vite.
- React Router.
- Tailwind CSS.
- TanStack Query.
- React Hook Form.
- Zod.
- Axios.
- Recharts.
- Lucide React.

Organizar a interface em:
- Área pública.
- Área do cliente.
- Área administrativa.

Adotar tema escuro e Design System próprio.

O frontend não será autoridade sobre regras financeiras, autorização ou segurança.

### Consequências

- Interface consistente.
- Separação entre apresentação e regras de negócio.
- Facilidade de evolução.
- Necessidade de manter contratos e estados de interface alinhados à API.

---

## Regras de manutenção dos ADRs

1. ADRs aceitos não devem ser alterados silenciosamente.
2. Toda mudança relevante deve gerar revisão documentada ou novo ADR.
3. ADRs substituídos devem permanecer no histórico com status de substituídos.
4. Novas decisões devem apresentar contexto, alternativas, justificativa e consequências.
5. O usuário deve aprovar mudanças relevantes antes da adoção.
6. Os documentos técnicos devem permanecer consistentes com os ADRs aceitos.

## Registro complementar — decisões aprovadas na consolidação técnica v1.1

O documento [MyBank — Consolidação das Decisões Técnicas Complementares](MyBank%20%E2%80%94%20Consolida%C3%A7%C3%A3o%20das%20Decis%C3%B5es%20T%C3%A9cnicas%20Complementares.md), versão 1.1, é a fonte aprovada para CPF e sua proteção, snapshot contratual de empréstimos, estornos administrativos e reprocessamento da Outbox. Este registro serve à rastreabilidade e não altera o status geral deste catálogo. A harmonização posterior à revisão registra `FAILED` como estado terminal oficial da Outbox.

As decisões devem ser refletidas nos documentos de requisitos, arquitetura, dados, contratos REST, OpenAPI e frontend. `FAILED` é o estado terminal oficial e elegível da Outbox; `DEAD_LETTER` identifica somente encaminhamento ou armazenamento operacional.
