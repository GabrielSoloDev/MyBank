# MyBank — Revisão da Modelagem do Banco de Dados
## Complemento técnico à versão 1.0 do DER e Dicionário de Dados

**Versão:** 1.1 — Proposta de revisão  
**Banco de dados:** PostgreSQL  
**Referência:** Documento de Modelagem do Banco de Dados — versão 1.0

Este documento complementa a versão 1.0. Todas as entidades, campos e regras não mencionados aqui permanecem conforme a versão original. As alterações abaixo devem ser incorporadas ao DER e ao dicionário de dados antes da aprovação definitiva da modelagem.

---

# 1. Separação entre usuário, cliente e conta

## 1.1. Separação dos estados

O estado de autenticação do usuário não deve ser confundido com a situação cadastral do cliente ou com a situação operacional da conta bancária.

Serão três controles independentes:

| Entidade | Responsabilidade |
|---|---|
| `users.status` | Controlar a situação das credenciais e do acesso ao sistema. |
| `customer_profiles.status` | Controlar a situação cadastral e operacional do cliente. |
| `bank_accounts.status` | Controlar a situação operacional da conta bancária. |

O backend deverá considerar os três contextos ao autorizar operações.

Um usuário com credenciais ativas não poderá realizar operações financeiras se o seu cadastro de cliente estiver bloqueado, suspenso ou encerrado. Da mesma forma, um cliente ativo não poderá movimentar uma conta bloqueada ou encerrada.

## 1.2. Alteração em `customer_profiles`

Adicionar o campo:

| Campo | Tipo | Regras |
|---|---|---|
| `status` | VARCHAR(20) | NN, estado cadastral do cliente |

Estados propostos:

- `PENDING`: cadastro ainda não liberado para operações.
- `ACTIVE`: cliente ativo.
- `BLOCKED`: cliente temporariamente impedido de operar.
- `SUSPENDED`: cliente suspenso por decisão administrativa.
- `CLOSED`: relacionamento encerrado, estado terminal.

A situação `CLOSED` não elimina o histórico financeiro nem as obrigações existentes.

## 1.3. Nova tabela `customer_status_history`

Registra alterações no estado cadastral do cliente.

| Campo | Tipo | Regras |
|---|---|---|
| `id` | UUID | PK, NN |
| `customer_id` | UUID | FK → `customer_profiles.id`, NN |
| `previous_status` | VARCHAR(20) | NULL na criação |
| `new_status` | VARCHAR(20) | NN |
| `changed_by_user_id` | UUID | FK → `users.id`, NULL para eventos sistêmicos |
| `reason` | TEXT | NULL, obrigatório nas alterações administrativas sensíveis |
| `created_at` | TIMESTAMPTZ | NN |

Regras:

- Toda alteração de estado deverá gerar histórico.
- O histórico não poderá ser alterado por operações administrativas comuns.
- Alterações administrativas sensíveis exigem justificativa e auditoria.
- A transição deverá ser validada pelo domínio.

## 1.4. Nova tabela `bank_account_status_history`

Registra alterações no estado das contas.

| Campo | Tipo | Regras |
|---|---|---|
| `id` | UUID | PK, NN |
| `account_id` | UUID | FK → `bank_accounts.id`, NN |
| `previous_status` | VARCHAR(20) | NULL na criação |
| `new_status` | VARCHAR(20) | NN |
| `changed_by_user_id` | UUID | FK → `users.id`, NULL para eventos sistêmicos |
| `reason` | TEXT | NULL, obrigatório nas alterações administrativas sensíveis |
| `created_at` | TIMESTAMPTZ | NN |

## 1.5. Transições de clientes

Proposta de máquina de estados:

- `PENDING` → `ACTIVE`, `BLOCKED`, `CLOSED`.
- `ACTIVE` → `BLOCKED`, `SUSPENDED`, `CLOSED`.
- `BLOCKED` → `ACTIVE`, `SUSPENDED`, `CLOSED`.
- `SUSPENDED` → `ACTIVE`, `BLOCKED`, `CLOSED`.
- `CLOSED` → sem transições.

O encerramento do cliente não poderá apagar empréstimos, parcelas, transações, lançamentos contábeis ou auditoria.

A reativação de um cliente encerrado não será permitida diretamente. Eventual novo relacionamento deverá seguir um processo próprio, preservando o histórico anterior.

## 1.6. Transições de contas

Estados:

`PENDING`, `ACTIVE`, `BLOCKED`, `SUSPENDED`, `CLOSED`.

Transições propostas:

- `PENDING` → `ACTIVE`, `CLOSED`.
- `ACTIVE` → `BLOCKED`, `SUSPENDED`, `CLOSED`.
- `BLOCKED` → `ACTIVE`, `SUSPENDED`, `CLOSED`.
- `SUSPENDED` → `ACTIVE`, `BLOCKED`, `CLOSED`.
- `CLOSED` → sem transições.

Condições para encerramento:

- Saldo igual a zero.
- Nenhuma operação pendente que dependa da conta.
- Cartões vinculados bloqueados ou cancelados.
- Consentimento de todos os titulares ativos, no caso de conta conjunta.
- Preservação das obrigações financeiras existentes.

Empréstimos vinculados ao cliente não impedem automaticamente o encerramento da conta, desde que exista mecanismo de pagamento das parcelas independente dela.

---

# 2. Estornos administrativos

## 2.1. Política da primeira versão

A primeira versão permitirá somente **estorno integral** de transações elegíveis.

Não haverá estorno parcial nesta versão.

O estorno deverá gerar uma nova transação e novos lançamentos contábeis compensatórios. Os registros originais permanecerão imutáveis.

## 2.2. Alteração em `transaction_reversals`

Estrutura revisada:

| Campo | Tipo | Regras |
|---|---|---|
| `id` | UUID | PK, NN |
| `original_transaction_id` | UUID | FK → `transactions.id`, NN, UK |
| `reversal_transaction_id` | UUID | FK → `transactions.id`, NN, UK |
| `requested_by_user_id` | UUID | FK → `users.id`, NN |
| `reason` | TEXT | NN |
| `created_at` | TIMESTAMPTZ | NN |

A unicidade de `original_transaction_id` impede mais de um estorno integral da mesma transação por este mecanismo.

## 2.3. Regras de elegibilidade

Um estorno administrativo deverá:

1. Referenciar uma transação original existente.
2. Ser permitido somente para transações concluídas e contabilizadas.
3. Impedir estorno duplicado.
4. Impedir estorno de outro estorno.
5. Gerar uma nova transação de compensação.
6. Gerar lançamentos contábeis inversos aos da operação original.
7. Preservar a referência entre operação original e compensatória.
8. Exigir justificativa e identificação do administrador responsável.
9. Registrar a ação em `audit_events`.
10. Ser executado atomicamente.

A elegibilidade também deverá considerar o tipo da operação. Operações com tratamento financeiro próprio, como pagamentos de parcelas e empréstimos, não poderão ser estornadas indiscriminadamente pelo fluxo genérico.

## 2.4. Limites administrativos

- Qualquer ADMIN poderá solicitar um estorno permitido.
- Não haverá segunda aprovação administrativa.
- Será obrigatória confirmação adicional e justificativa.
- O administrador não poderá editar diretamente o saldo.
- Não será permitido excluir ou modificar lançamentos originais.
- A disponibilidade financeira necessária para a compensação deverá ser verificada antes da execução.
- Caso a compensação não possa ser realizada com segurança, a operação deverá ser rejeitada e registrada para análise.

A definição de procedimentos específicos para correção de pagamentos de parcelas e contratos de empréstimo permanece sujeita às regras próprias desses módulos.

---

# 3. Pagamento de empréstimos após encerramento de conta

## 3.1. Regra arquitetural

O pagamento de parcelas não poderá depender exclusivamente de uma conta bancária MyBank ativa.

O cliente poderá manter obrigações financeiras após o encerramento ou bloqueio da conta bancária.

Na primeira versão, será utilizado um mecanismo de pagamento inteiramente simulado, sem integração com bancos, Pix, boletos ou instituições externas.

## 3.2. Nova tabela `loan_installment_payments`

Registra pagamentos simulados de parcelas.

| Campo | Tipo | Regras |
|---|---|---|
| `id` | UUID | PK, NN |
| `loan_installment_id` | UUID | FK → `loan_installments.id`, NN |
| `transaction_id` | UUID | FK → `transactions.id`, NN, UK |
| `amount` | NUMERIC(19,2) | NN, maior que zero |
| `payment_method` | VARCHAR(30) | NN, inicialmente `SIMULATED_MANUAL` |
| `status` | VARCHAR(20) | NN |
| `paid_at` | TIMESTAMPTZ | NULL |
| `created_at` | TIMESTAMPTZ | NN |

Estados propostos:

- `PENDING`
- `PROCESSING`
- `COMPLETED`
- `FAILED`
- `CANCELLED`

## 3.3. Regras de pagamento

- O pagamento será associado diretamente à parcela.
- Não será obrigatória uma conta bancária ativa.
- A transação de pagamento poderá possuir `account_id` nulo.
- A identificação do cliente e do empréstimo deverá permanecer disponível.
- A liquidação da parcela e a contabilização deverão ocorrer atomicamente.
- O pagamento deverá ser idempotente.
- A mesma parcela não poderá ser liquidada duas vezes.
- O valor pago deverá respeitar o valor efetivamente devido.
- A parcela somente será marcada como `PAID` após a contabilização bem-sucedida.

A primeira versão considera a quitação integral de cada parcela. Pagamentos parciais ficam fora do escopo até aprovação específica.

## 3.4. Contabilização

O pagamento deverá utilizar contas contábeis próprias de liquidação simulada.

Exemplo conceitual:

| Conta contábil | Direção | Valor |
|---|---|---:|
| Conta de compensação de recebimentos | DEBIT | Valor recebido |
| Conta de obrigação/recebimento do empréstimo | CREDIT | Valor recebido |

A estrutura contábil definitiva deverá separar adequadamente principal, juros e demais componentes aplicáveis ao contrato.

O encerramento da conta bancária não poderá apagar ou cancelar parcelas ainda devidas.

---

# 4. Transactional Outbox — Falhas e reprocessamento

## 4.1. Estados revisados de `outbox_events`

A máquina de estados oficial da Outbox é:

- `PENDING`
- `PROCESSING`
- `FAILED` — estado terminal para falha após esgotamento das tentativas ou classificação definitiva.
- `PROCESSED` — processamento concluído com sucesso.

`DEAD_LETTER` não é estado. É o encaminhamento ou armazenamento operacional de eventos cujo processamento esgotou as tentativas; esses eventos permanecem com estado `FAILED`.

## 4.2. Estrutura revisada de `outbox_events`

| Campo | Tipo | Regras |
|---|---|---|
| `id` | UUID | PK, NN |
| `aggregate_type` | VARCHAR(80) | NN |
| `aggregate_id` | UUID | NN |
| `event_type` | VARCHAR(100) | NN |
| `payload` | JSONB | NN |
| `status` | VARCHAR(20) | NN |
| `attempt_count` | INTEGER | NN, padrão 0 |
| `processing_cycle` | INTEGER | NN, padrão 1 |
| `reprocess_count` | INTEGER | NN, padrão 0 |
| `created_at` | TIMESTAMPTZ | NN |
| `last_attempt_at` | TIMESTAMPTZ | NULL |
| `next_attempt_at` | TIMESTAMPTZ | NULL |
| `processed_at` | TIMESTAMPTZ | NULL |
| `locked_at` | TIMESTAMPTZ | NULL |
| `locked_until` | TIMESTAMPTZ | NULL |
| `worker_id` | VARCHAR(100) | NULL |
| `dead_lettered_at` | TIMESTAMPTZ | NULL, instante do encaminhamento/armazenamento operacional; não representa estado |
| `last_error` | TEXT | NULL |

## 4.3. Política de tentativas

- Cada ciclo terá no máximo cinco tentativas.
- O intervalo entre tentativas aumentará progressivamente.
- O agendamento será armazenado em `next_attempt_at`.
- O processamento deverá ser idempotente.
- Falhas temporárias poderão ser tentadas novamente.
- Falhas definitivas poderão receber estado `FAILED` e ser encaminhadas ao armazenamento operacional dead-letter, conforme classificação técnica.
- Ao esgotar as tentativas, o evento receberá estado `FAILED`; seu encaminhamento operacional não altera o estado persistido.
- O histórico de tentativas deverá ser preservado.

A duração exata dos intervalos de espera será definida na configuração técnica da implementação, sem alterar o limite de cinco tentativas aprovado.

## 4.4. Nova tabela `outbox_event_attempts`

Registra individualmente cada tentativa de processamento.

| Campo | Tipo | Regras |
|---|---|---|
| `id` | UUID | PK, NN |
| `outbox_event_id` | UUID | FK → `outbox_events.id`, NN |
| `processing_cycle` | INTEGER | NN |
| `attempt_number` | INTEGER | NN |
| `worker_id` | VARCHAR(100) | NULL |
| `status` | VARCHAR(20) | NN |
| `error_message` | TEXT | NULL |
| `started_at` | TIMESTAMPTZ | NN |
| `finished_at` | TIMESTAMPTZ | NULL |
| `created_at` | TIMESTAMPTZ | NN |

Restrição:

`UNIQUE(outbox_event_id, processing_cycle, attempt_number)`.

## 4.5. Recuperação de processamento interrompido

O worker deverá utilizar controle de posse temporária do processamento.

- Ao iniciar, registra `locked_at`, `locked_until` e `worker_id`.
- Se o worker falhar e a posse expirar, o evento poderá ser recuperado.
- Eventos com posse expirada em `PROCESSING` deverão retornar ao fluxo de processamento.
- A recuperação deverá respeitar o limite de tentativas.
- A recuperação não poderá criar processamento financeiro duplicado.

A idempotência do consumidor é obrigatória, pois a entrega de eventos poderá ocorrer mais de uma vez.

## 4.6. Reprocessamento administrativo

Somente eventos elegíveis com estado `FAILED` poderão ser reprocessados por ADMIN autorizado. O encaminhamento ou armazenamento dead-letter, por si só, não torna um evento elegível.

O reprocessamento deverá:

1. Exigir justificativa.
2. Registrar o administrador responsável em `audit_events`.
3. Incrementar `reprocess_count`.
4. Incrementar `processing_cycle`.
5. Reiniciar `attempt_count` para o novo ciclo.
6. Preservar todas as tentativas anteriores.
7. Retornar o evento para `PENDING`.

O reprocessamento não poderá modificar o conteúdo original do evento sem um procedimento de correção rastreável.

Não haverá edição manual de payload ou alteração direta de registros pelo painel administrativo.

---

# 5. Ajustes complementares ao DER

## 5.1. Cardinalidade de provedores de pagamento

Corrigir o relacionamento:

- Um `payment_provider` pode estar associado a vários `payments`.
- Cada `payment` utiliza um único `payment_provider`.

Representação:

`PAYMENT_PROVIDERS ||--o{ PAYMENTS`

## 5.2. Histórico de estados

Adicionar ao DER:

- `CUSTOMER_PROFILES ||--o{ CUSTOMER_STATUS_HISTORY`
- `BANK_ACCOUNTS ||--o{ BANK_ACCOUNT_STATUS_HISTORY`
- `OUTBOX_EVENTS ||--o{ OUTBOX_EVENT_ATTEMPTS`

Esses relacionamentos tornam explícita a preservação do histórico de estados e das tentativas de processamento.

## 5.3. Empréstimos e pagamentos

Adicionar:

`LOAN_INSTALLMENTS ||--o{ LOAN_INSTALLMENT_PAYMENTS`

`TRANSACTIONS ||--o| LOAN_INSTALLMENT_PAYMENTS`

O relacionamento permite registrar o pagamento de uma parcela sem exigir vínculo com uma conta bancária ativa.

## 5.4. Estornos

O relacionamento entre `transactions` e `transaction_reversals` deverá representar:

- Uma transação original poderá ter, no máximo, um estorno integral pelo fluxo genérico.
- Cada registro de estorno referencia uma transação original.
- Cada estorno referencia uma transação compensatória distinta.
- A transação compensatória não poderá ser utilizada como origem de outro estorno genérico.

---

# 6. Ajustes nas restrições e índices

Adicionar às restrições:

| Entidade | Restrição |
|---|---|
| `customer_profiles` | Estado cadastral obrigatório e controlado |
| `bank_accounts` | Estado operacional obrigatório e controlado |
| `customer_status_history` | Histórico associado a cliente existente |
| `bank_account_status_history` | Histórico associado a conta existente |
| `transaction_reversals` | Uma reversão integral por transação original |
| `transaction_reversals` | Transação compensatória única |
| `loan_installment_payments` | Pagamento associado a uma parcela existente |
| `loan_installment_payments` | Transação de pagamento única |
| `outbox_events` | Tentativas não negativas |
| `outbox_events` | Ciclo de processamento positivo |
| `outbox_event_attempts` | Tentativa única por evento, ciclo e número |

Índices adicionais:

| Tabela | Índice |
|---|---|
| `customer_profiles` | Estado cadastral |
| `customer_status_history` | Cliente e data |
| `bank_account_status_history` | Conta e data |
| `transaction_reversals` | Transação original |
| `loan_installment_payments` | Parcela e estado |
| `outbox_events` | Estado e próxima tentativa |
| `outbox_events` | Estado e validade do bloqueio |
| `outbox_event_attempts` | Evento e ciclo |

---

# 7. Atualização da organização por módulos

| Módulo | Novas entidades ou alterações |
|---|---|
| Identity | Sem alteração estrutural obrigatória |
| Customer | `customer_profiles.status`, `customer_status_history` |
| Account | `bank_account_status_history` |
| Transaction | Regras revisadas de `transaction_reversals` |
| Loan | `loan_installment_payments` |
| Infrastructure | `outbox_event_attempts`, estrutura revisada de `outbox_events` |
| Admin / Audit | Auditoria obrigatória de estornos e reprocessamentos |

As novas tabelas deverão ser incluídas no DER, dicionário de dados, migrações Flyway e testes de integridade.

---

# 8. Atualização das migrações Flyway

A sequência V001–V016 da versão 1.0 permanece como proposta inicial, mas deverá ser ajustada para contemplar as novas entidades e dependências.

Migrações adicionais propostas:

| Migração | Conteúdo |
|---|---|
| V017 | Histórico de estados de clientes e contas |
| V018 | Estrutura de pagamentos de parcelas de empréstimos |
| V019 | Histórico de tentativas da Outbox |
| V020 | Restrições e índices complementares |

A alteração de `transaction_reversals` e `outbox_events` deverá ser incluída em migrações incrementais, respeitando a regra de não editar migrações já aplicadas em ambientes compartilhados.

A numeração definitiva deverá ser ajustada após a organização das dependências reais.

---

# 9. Pontos que permanecem para validação

As quatro propostas aprovadas foram incorporadas. Os pontos abaixo são complementares e ainda não devem ser tratados como decisões definitivas:

1. **Transições de clientes:** validar se `CLOSED` será sempre terminal ou se haverá processo formal de novo relacionamento.
2. **Estornos:** validar a lista definitiva de tipos de transação elegíveis e o tratamento específico de operações de empréstimo e pagamentos de parcelas.
3. **Pagamento de parcelas:** validar se a quitação integral por parcela será suficiente para a primeira versão, sem pagamentos parciais.
4. **Outbox:** definir os intervalos exatos de espera progressiva e os critérios de classificação de falhas recuperáveis e definitivas.
5. **Auditoria:** validar quais tipos de consulta sensível terão eventos específicos e quais dados poderão ser registrados sem exposição de informações pessoais.
6. **Contratos de empréstimo:** validar se será necessário armazenar um demonstrativo de CET na primeira versão.

---

# 10. Critérios de aceite complementares

- [ ] Estado de usuário separado do estado cadastral do cliente.
- [ ] Estado de cliente separado do estado da conta bancária.
- [ ] Histórico de transições de clientes e contas.
- [ ] Encerramento preserva histórico financeiro e obrigações.
- [ ] Estornos genéricos são integrais e não duplicáveis.
- [ ] Estornos preservam os lançamentos originais.
- [ ] Ações administrativas de estorno exigem justificativa e auditoria.
- [ ] Pagamentos de parcelas não dependem de conta ativa.
- [ ] Pagamentos de parcelas geram contabilização própria.
- [ ] Eventos possuem histórico individual de tentativas.
- [ ] Cinco tentativas por ciclo.
- [ ] Eventos esgotados possuem estado `FAILED` e podem ser encaminhados ao armazenamento operacional dead-letter.
- [ ] Processamentos interrompidos podem ser recuperados.
- [ ] Reprocessamentos administrativos são auditados.
- [ ] Consumidores possuem idempotência.
- [ ] DER e dicionário de dados refletem as novas relações.

---

**Status:** proposta de revisão técnica da modelagem versão 1.0.

**Próxima etapa:** incorporar os ajustes aprovados ao documento principal e validar o DER completo, suas cardinalidades, restrições e dicionário de dados antes de iniciar a documentação dos contratos REST.

Nenhuma implementação funcional está autorizada por este documento.

# 8. Pendências de consolidação do DER e do dicionário

A consolidação complementar aprovada exige CPF protegido por AES-256-GCM, HMAC-SHA-256 independente e metadados de versionamento necessários à rotação. Exige também a representação persistida do snapshot contratual imutável dos empréstimos e a preservação do histórico individual de tentativas da Outbox.

O DER e o dicionário de dados versão 1.0 referenciados no início deste documento não estão presentes no repositório. Solicita-se o fornecimento dos documentos originais aprovados para concluir a auditoria. Até que estejam disponíveis, este complemento não inventa nem reconstrói nomes de colunas, tipos, restrições ou relacionamentos para CPF e snapshot. Esses elementos precisam ser conferidos e incorporados aos artefatos-base antes de considerar a modelagem consolidada.

**Harmonização após revisão:** `FAILED` é o estado terminal oficial e o único estado terminal elegível para reprocessamento, sujeito aos demais critérios. `DEAD_LETTER` designa somente o encaminhamento ou armazenamento operacional de eventos esgotados e não aparece como estado alternativo. O DER e o dicionário de dados originais continuam ausentes; a revisão de seus campos e metadados permanece pendente até que os documentos-base aprovados sejam disponibilizados.
