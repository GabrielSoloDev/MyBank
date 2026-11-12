# MyBank — Definição dos Parâmetros Operacionais

| Campo | Valor |
| --- | --- |
| Identificador | MB-OPS-001 |
| Classe | Documento oficial da biblioteca MyBank |
| Original preservado | [MyBank — Definição dos Parâmetros Operacionais.md](../99-fontes-originais/MyBank%20—%20Definição%20dos%20Parâmetros%20Operacionais.md) |
| Etapa | Definição de parâmetros pendentes |
| Versão | 1.0 — Proposta para aprovação |
| Status | Aguardando revisão |
| Moeda | BRL (Real brasileiro) |

## Sumário

- [1. Limites financeiros por operação](#1-limites-financeiros-por-operacao)
- [2. Parâmetros dos empréstimos](#2-parametros-dos-emprestimos)
- [3. Validade das ofertas de empréstimo](#3-validade-das-ofertas-de-emprestimo)
- [4. Pagamento das parcelas de empréstimos](#4-pagamento-das-parcelas-de-emprestimos)
- [5. Estornos e correções financeiras](#5-estornos-e-correcoes-financeiras)
- [6. Processamento de pagamentos agendados](#6-processamento-de-pagamentos-agendados)
- [7. Política de risco e autenticação adicional](#7-politica-de-risco-e-autenticacao-adicional)
- [8. Obrigações em contas conjuntas](#8-obrigacoes-em-contas-conjuntas)
- [9. Administração dos parâmetros](#9-administracao-dos-parametros)
- [10. Critérios de aceite](#10-criterios-de-aceite)
- [11. Próxima etapa](#11-proxima-etapa)

## 1. Limites financeiros por operação

### 1.1 Proposta de limites iniciais

Os valores abaixo são limites demonstrativos para a primeira versão do MyBank. Todos poderão ser alterados por administradores autorizados, respeitando limites de segurança definidos pelo sistema.

| Operação | Limite por operação | Limite diário |
| --- | ---: | ---: |
| Transferência | R$ 10.000,00 | R$ 20.000,00 |
| Depósito simulado | R$ 20.000,00 | R$ 50.000,00 |
| Saque simulado | R$ 5.000,00 | R$ 10.000,00 |
| Pagamento | R$ 10.000,00 | R$ 20.000,00 |
| Compra com cartão | R$ 5.000,00 | R$ 10.000,00 |

### 1.2 Regras

- Valor mínimo de qualquer operação financeira: R$ 0,01.
- Limites devem ser configuráveis por tipo de operação.
- O limite diário considera o total de operações concluídas no dia.
- Operações rejeitadas ou canceladas não consomem limite.
- Operações em processamento devem ser consideradas para evitar ultrapassagem por concorrência.
- Os limites são aplicados por conta, e não individualmente por titular.
- Limites específicos de cartão também devem respeitar o limite de gasto disponível.
- Administradores podem alterar parâmetros mediante justificativa e auditoria.
- Alterações não afetam retroativamente operações já concluídas.

## 2. Parâmetros dos empréstimos

### 2.1 Proposta inicial

| Parâmetro | Valor proposto |
| --- | ---: |
| Taxa mensal inicial | 2,00% |
| Taxa mensal mínima configurável | 0,50% |
| Taxa mensal máxima configurável | 5,00% |
| Valor mínimo | R$ 500,00 |
| Valor máximo | R$ 20.000,00 |
| Prazo mínimo | 6 meses |
| Prazo máximo | 48 meses |
| Comprometimento máximo da renda | 30% |

### 2.2 Capacidade de pagamento

O comprometimento de renda será calculado considerando:

- Renda mensal declarada.
- Parcelas de empréstimos ativos.
- Novas parcelas solicitadas.
- Obrigações vencidas, conforme política definida.

Fórmula proposta:

\[
\text{Comprometimento} =
\frac{\text{Parcelas mensais totais}}{\text{Renda mensal}}
\times 100
\]

O comprometimento total não poderá ultrapassar 30% da renda mensal considerada na análise.

### 2.3 Regras

- Sistema de amortização Price.
- Taxa de juros armazenada no contrato como snapshot.
- Parcelas calculadas no backend utilizando `BigDecimal`.
- Arredondamento monetário em duas casas decimais.
- O valor final da última parcela poderá receber ajuste de centavos para fechar o saldo do contrato.
- Alterações posteriores nas taxas não modificam contratos existentes.
- Múltiplos empréstimos são permitidos, respeitando o comprometimento de renda.
- Aprovação depende de análise administrativa.
- O administrador deve registrar justificativa.
- A aprovação não pode ser utilizada após o vencimento da oferta.

## 3. Validade das ofertas de empréstimo

### Proposta

- Validade padrão: **7 dias corridos**.
- A contagem começa no momento da aprovação.
- A data e hora de expiração são armazenadas em `expires_at`.
- A contratação deve validar a validade no backend.
- Oferta expirada não pode ser contratada.
- Oferta expirada não pode ser reativada diretamente.
- Nova contratação exige nova análise ou emissão de nova oferta, conforme política administrativa.

A expiração deve ser verificada no momento da contratação, independentemente do estado apresentado no frontend.

## 4. Pagamento das parcelas de empréstimos

### 4.1 Modelo proposto

O pagamento será inteiramente simulado dentro do MyBank.

- Cada contrato terá datas de vencimento definidas.
- O sistema tentará realizar o débito na conta ativa indicada para pagamento.
- O pagamento será realizado por meio do Ledger.
- O processamento será automático e simulado.
- O cliente poderá consultar parcelas, vencimentos e situação.
- Não haverá débito em bancos externos.

### 4.2 Regras de processamento

1. O sistema identifica parcelas com vencimento na data.
2. Verifica a situação do contrato.
3. Verifica a conta indicada para pagamento.
4. Bloqueia os registros necessários.
5. Revalida saldo e situação.
6. Se houver saldo suficiente, registra os lançamentos.
7. Atualiza a parcela para `PAID`.
8. Se não houver saldo suficiente, atualiza a parcela para `OVERDUE`.
9. Registra histórico, auditoria e notificação.

### 4.3 Obrigações após encerramento de conta

- O empréstimo é uma obrigação pessoal do contratante.
- O encerramento da conta não extingue o empréstimo.
- O titular poderá indicar outra conta ativa elegível para pagamento.
- Se não possuir conta ativa, o pagamento ficará pendente até que exista um mecanismo de pagamento simulado disponível.
- A obrigação permanece registrada e pode gerar atraso.
- Outros titulares da conta conjunta não assumem automaticamente a dívida pessoal do contratante.

## 5. Estornos e correções financeiras

### 5.1 Regras gerais

- Lançamentos contabilizados são imutáveis.
- Correções devem utilizar estorno ou compensação.
- Todo estorno deve referenciar a operação original.
- Estornos devem ser idempotentes.
- Não é permitido estornar uma mesma operação integralmente mais de uma vez.
- Toda ação administrativa de estorno exige justificativa e auditoria.

### 5.2 Proposta por operação

| Operação | Regra proposta |
| --- | --- |
| Compra com cartão | Estorno integral permitido, mediante validação |
| Transferência | Sem cancelamento após conclusão; retorno por nova transferência |
| Depósito | Correção administrativa por compensação |
| Saque | Correção administrativa por compensação |
| Pagamento | Estorno administrativo quando permitido pela regra do serviço fictício |
| Parcela de empréstimo | Correção administrativa por compensação |

### 5.3 Estornos de compras

- Cliente pode solicitar estorno de uma compra simulada.
- Solicitação não implica aprovação automática.
- Backend verifica a compra original e seu estado.
- Estorno aprovado recompõe saldo e limite do cartão.
- Não permitir estorno de compra inexistente ou já estornada.
- Registrar o motivo e o responsável pela decisão.

### 5.4 Limites administrativos

- Administradores não podem modificar diretamente o saldo.
- Estornos são operações financeiras formais.
- Estornos parciais ficam fora do escopo inicial.
- Correções devem preservar o histórico original.

## 6. Processamento de pagamentos agendados

### Proposta

- Frequência de execução: a cada 1 minuto.
- Fuso horário de referência: `America/Sao_Paulo`.
- O horário de vencimento é armazenado de forma consistente, com conversão para o fuso de apresentação.
- Processamento ocorre por tarefa agendada no backend.
- Não utilizar Kafka ou RabbitMQ inicialmente.
- Outbox e processamento persistente no PostgreSQL.

### 6.1 Tentativas

Para falhas técnicas transitórias:

| Tentativa | Intervalo |
| --- | --- |
| Primeira | Imediata |
| Segunda | Após 1 minuto |
| Terceira | Após 5 minutos |
| Quarta | Após 15 minutos |

Após a última tentativa, a falha deve ser registrada para análise.

### 6.2 Regras

- Saldo insuficiente não é considerado falha técnica transitória.
- Saldo insuficiente resulta em `FAILED`, sem contabilização.
- Falha técnica não pode gerar lançamentos parciais.
- Operações já concluídas não podem ser executadas novamente.
- Processadores devem ser idempotentes.
- Processamento concorrente deve impedir que duas instâncias executem o mesmo pagamento.
- Cancelamento só é permitido nos estados previstos.

## 7. Política de risco e autenticação adicional

### 7.1 Regras gerais

O sistema deverá utilizar uma política configurável para determinar quando solicitar autenticação adicional.

A autenticação adicional não substitui autenticação, autorização ou validação de titularidade.

### 7.2 Proposta inicial

| Operação ou situação | Exigência proposta |
| --- | --- |
| Transferência a partir de R$ 5.000,00 | 2FA se habilitado; caso contrário, reautenticação |
| Alteração de senha | Reautenticação |
| Ativação ou desativação do 2FA | Reautenticação |
| Alteração de dados sensíveis | Reautenticação |
| Criação de cartão virtual | Conforme política de risco |
| Compra com cartão | Conforme política de risco |
| Encerramento de conta | Autenticação adicional |
| Ações administrativas críticas | 2FA obrigatório e justificativa |
| Alteração de parâmetros financeiros | 2FA obrigatório e justificativa |
| Bloqueio ou desativação de administrador | 2FA obrigatório e justificativa |

### 7.3 Regras complementares

- ADMIN sempre utiliza 2FA.
- CUSTOMER pode habilitar 2FA voluntariamente.
- CUSTOMER sem 2FA utiliza reautenticação de senha nas ações que exigirem confirmação adicional.
- Os limites e critérios de risco devem ser configuráveis.
- Tentativas excessivas devem ser limitadas.
- Desafios devem ter validade e uso único.
- Ações críticas devem ser registradas na auditoria.

## 8. Obrigações em contas conjuntas

### 8.1 Natureza das obrigações

- Empréstimos são pessoais, vinculados ao cliente contratante.
- O encerramento da conta conjunta não transfere automaticamente obrigações pessoais aos demais titulares.
- Outros titulares não respondem automaticamente por empréstimos contratados individualmente.
- Transferências, pagamentos e demais operações identificam o titular responsável pela solicitação.

### 8.2 Saída de titular

- A saída voluntária não elimina obrigações pessoais.
- A saída não autoriza retirada unilateral de patrimônio compartilhado.
- Percentuais de participação somente serão utilizados quando estiverem definidos conforme as regras da conta.
- Sem percentuais definidos, o patrimônio permanece compartilhado, sem atribuição individual automática.
- A saída deve respeitar o mínimo de titulares exigido.

### 8.3 Encerramento conjunto

- Exige consentimento de todos os titulares ativos.
- Exige saldo igual a zero.
- Exige inexistência de operações pendentes.
- Cartões vinculados devem ser bloqueados ou cancelados.
- Obrigações pessoais dos titulares continuam existindo.
- O encerramento não elimina histórico financeiro.

### 8.4 Ponto operacional

Se um titular possuir empréstimo vinculado à conta utilizada para pagamento, deverá existir uma alternativa de pagamento antes do encerramento dessa conta. A obrigação continuará vinculada ao titular, não aos demais participantes.

---

## 9. Administração dos parâmetros

Todos os parâmetros configuráveis devem possuir:

- Identificador.
- Nome.
- Descrição.
- Valor vigente.
- Tipo de dado.
- Limites permitidos.
- Data de início da vigência.
- Data da última alteração.
- Administrador responsável.
- Justificativa.
- Histórico de versões.

### Regras

1. Alterações devem ser validadas pelo backend.
2. Alterações críticas exigem 2FA e justificativa.
3. Alterações devem gerar auditoria.
4. Não permitir valores fora dos limites estabelecidos.
5. Mudanças devem ter efeito prospectivo, salvo regra explicitamente definida.
6. Contratos de empréstimos existentes preservam seus snapshots.
7. Operações concluídas preservam as condições originais.

## 10. Critérios de aceite

- Limites financeiros definidos por operação.
- Parâmetros de empréstimo definidos.
- Validade das ofertas definida.
- Pagamento simulado de parcelas definido.
- Regras de estorno definidas.
- Frequência e tentativas de processamento definidas.
- Política inicial de autenticação adicional definida.
- Responsabilidade por obrigações em contas conjuntas definida.
- Parâmetros administrativos possuem histórico e auditoria.
- Nenhuma regra financeira depende exclusivamente do frontend.

## 11. Próxima etapa

Após aprovação, os parâmetros serão congelados como referência funcional.

A próxima etapa será consolidar:

- ADRs das decisões arquiteturais e operacionais.
- Documentação técnica final.
- Requisitos funcionais e não funcionais.
- Arquitetura e diagramas.
- Contratos REST aprovados.
- DER e dicionário de dados aprovados.
- Fluxos de negócio aprovados.
- Parâmetros operacionais aprovados.
- Roadmap de implementação.

Somente após o fechamento dessa documentação será preparado o prompt mestre definitivo para o Codex.

**Status:** proposta para revisão e aprovação humana.
