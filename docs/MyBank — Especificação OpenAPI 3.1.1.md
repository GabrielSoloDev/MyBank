# MyBank — Especificação OpenAPI 3.1.1

**Versão da API:** 1.1  
**Versão OpenAPI:** 3.1.1  
**Status:** Proposta para revisão — consolidação documental em auditoria
**Formato:** YAML  
**Base path:** `/api/v1`  
**Formato de dados:** JSON / UTF-8  
**Moeda:** BRL  
**Fuso de negócio:** `America/Sao_Paulo`

## 1. Objetivo

Esta especificação formaliza os contratos REST do MyBank para permitir:

- Documentação interativa da API.
- Geração e validação de clientes HTTP.
- Validação automatizada de requisições e respostas.
- Testes de contrato.
- Verificação de compatibilidade entre frontend e backend.
- Rastreabilidade entre requisitos, endpoints e schemas.

A especificação deverá permanecer alinhada aos documentos aprovados de requisitos, arquitetura, DER, dicionário de dados e contratos REST v1.1.

## 2. Soluções incorporadas para os pontos de atenção

### 2.1. Limites administrativos de estorno

Os limites serão configuráveis em `system_settings`, sem valores numéricos padrão inventados.

A política deverá contemplar:

- Valor máximo por estorno.
- Valor acumulado máximo por período.
- Quantidade máxima de estornos por período.
- Categorias elegíveis.
- Idade máxima da transação original.
- Condições adicionais de elegibilidade.

**Regra de segurança:** a operação de estorno ficará indisponível enquanto qualquer parâmetro obrigatório estiver ausente, inválido ou inconsistente. Não será permitido interpretar ausência de configuração como limite ilimitado.

As alterações deverão ser auditadas, justificadas e submetidas à reautenticação administrativa.

### 2.2. Elegibilidade e situações excepcionais de estorno

Uma transação somente será elegível quando:

1. Estiver contabilizada e no estado `COMPLETED`.
2. Pertencer a uma categoria explicitamente habilitada.
3. Não possuir estorno anterior.
4. Não for ela própria um estorno.
5. Estiver dentro dos limites configurados.
6. Não violar as regras de integridade financeira.
7. Possuir dados suficientes para gerar a operação compensatória.
8. Não estar sujeita a bloqueio operacional ou legal que impeça a reversão.

O estorno será sempre integral na primeira versão.

**Transferências:** a reversão deverá compensar os lançamentos originais. Se a conta que recebeu os recursos não possuir saldo suficiente para suportar a compensação, o estorno será recusado. Não haverá saldo negativo nem alteração dos lançamentos originais.

**Depósitos e saques simulados:** a compensação utilizará as contas contábeis correspondentes.

**Pagamentos simulados:** a compensação respeitará a conta de origem e a contrapartida contábil.

**Compras simuladas com cartão:** a compensação deverá restaurar o saldo e o limite consumido, sem ultrapassar o limite original e respeitando o histórico do cartão.

**Empréstimos:** desembolsos e pagamentos de parcelas não serão aceitos pelo endpoint genérico. Terão fluxos específicos, com regras próprias de correção.

Uma falha de elegibilidade não poderá produzir lançamentos parciais.

### 2.3. Schemas e contratos completos

Os schemas serão centralizados em `components/schemas`, com:

- Tipos explícitos.
- Campos obrigatórios.
- Restrições de tamanho e formato.
- Enumerações de estados.
- Valores monetários como strings decimais.
- Datas em ISO 8601.
- UUIDs.
- Exemplos.
- Respostas padronizadas.

Os schemas de requisição não aceitarão propriedades desconhecidas em operações críticas (`additionalProperties: false`).

### 2.4. Catálogo de erros

Será utilizado um catálogo estável de códigos de erro, independente do texto apresentado ao usuário.

O formato será compatível com Problem Details, incluindo `code`, `correlationId`, `timestamp` e, quando necessário, `errors`.

### 2.5. Matriz de segurança por endpoint

Cada operação terá uma política explícita de:

- Autenticação.
- Perfil permitido.
- Titularidade ou escopo.
- 2FA.
- Reautenticação.
- Justificativa.
- Idempotência.

A autorização será aplicada no backend. A documentação OpenAPI não substitui as verificações de domínio.

### 2.6. Retenção e privacidade

Os prazos de retenção não serão inventados nesta especificação.

As políticas deverão ser configuráveis e documentadas conforme categoria de dado, finalidade, base legal e obrigação de retenção. Dados financeiros, Ledger e auditoria terão preservação histórica, respeitando as hipóteses legais de retenção e anonimização.

---

# 3. Arquivo OpenAPI

O conteúdo a seguir é a base formal da especificação. Os endpoints e schemas deverão ser mantidos em um único arquivo versionado, por exemplo:

`docs/04-api/openapi.yaml` (materializado nesta etapa)

```yaml
openapi: 3.1.1

info:
  title: MyBank API
  version: 1.1.0
  summary: API REST do banco digital fictício MyBank.
  description: |
    API REST para operações simuladas de um banco digital educacional.
    Não realiza transferências bancárias externas, Pix real, pagamentos
    reais, operações de câmbio ou compras reais com cartão.

    Regras fundamentais:
      - Moeda única: BRL.
      - Valores monetários são strings decimais.
      - Identificadores internos são UUID.
      - Operações financeiras respeitam partidas dobradas.
      - O Ledger é a fonte oficial dos saldos.
      - Operações financeiras contabilizadas são imutáveis.
      - Correções financeiras ocorrem por compensação.
      - Autorização e titularidade são verificadas no backend.
  contact:
    name: MyBank API
  license:
    name: Projeto educacional de portfólio

servers:
  - url: http://localhost:8080/api/v1
    description: Ambiente local de desenvolvimento
  - url: https://localhost/api/v1
    description: Ambiente de produção simulada

tags:
  - name: Authentication
  - name: Customers
  - name: Accounts
  - name: Account Invitations
  - name: Transactions
  - name: Transfers
  - name: Deposits
  - name: Withdrawals
  - name: Payments
  - name: Loans
  - name: Cards
  - name: Notifications
  - name: Administration
  - name: Audit
  - name: Privacy

security:
  - bearerAuth: []

paths:

  # ==========================================================
  # IDENTITY / AUTHENTICATION
  # ==========================================================

  /auth/register:
    post:
      tags: [Authentication]
      operationId: registerCustomer
      summary: Registrar cliente
      security: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/RegisterRequest'
            example:
              name: Maria da Silva
              email: maria@example.com
              documentNumber: "12345678900"
              password: "SenhaSegura123!"
      responses:
        '201':
          description: Cadastro realizado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CustomerResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/BusinessRuleError'
        '429':
          $ref: '#/components/responses/TooManyRequests'

  /auth/login:
    post:
      tags: [Authentication]
      operationId: login
      summary: Iniciar autenticação
      security: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/LoginRequest'
            example:
              email: maria@example.com
              password: "SenhaSegura123!"
      responses:
        '200':
          description: Autenticação concluída sem desafio adicional.
          headers:
            Set-Cookie:
              schema:
                type: string
              description: Refresh Token em cookie HttpOnly, Secure e SameSite.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthenticationResponse'
        '202':
          description: Autenticação depende de segundo fator.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoginChallengeResponse'
              example:
                data:
                  challengeId: 4b8d6b6d-3e0e-4f9e-8e7e-8f2a4e9a1234
                  challengeType: TOTP
                  status: PENDING
                  expiresAt: '2026-11-12T14:35:00-03:00'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '429':
          $ref: '#/components/responses/TooManyRequests'

  /auth/login/challenges/{challengeId}/verify:
    post:
      tags: [Authentication]
      operationId: verifyLoginChallenge
      summary: Validar desafio de autenticação
      security: []
      parameters:
        - $ref: '#/components/parameters/ChallengeId'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TotpVerificationRequest'
      responses:
        '200':
          description: Segundo fator validado.
          headers:
            Set-Cookie:
              schema:
                type: string
              description: Refresh Token em cookie seguro.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthenticationResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '404':
          $ref: '#/components/responses/NotFound'
        '409':
          $ref: '#/components/responses/Conflict'
        '429':
          $ref: '#/components/responses/TooManyRequests'

  /auth/refresh:
    post:
      tags: [Authentication]
      operationId: refreshSession
      summary: Renovar sessão
      security:
        - refreshCookie: []
      responses:
        '200':
          description: Token renovado e Refresh Token rotacionado.
          headers:
            Set-Cookie:
              schema:
                type: string
              description: Novo Refresh Token.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccessTokenResponse'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '409':
          $ref: '#/components/responses/Conflict'

  /auth/logout:
    post:
      tags: [Authentication]
      operationId: logout
      summary: Encerrar sessão atual
      security:
        - bearerAuth: []
          refreshCookie: []
      responses:
        '204':
          description: Sessão encerrada e Refresh Token revogado.
        '401':
          $ref: '#/components/responses/Unauthorized'

  /auth/logout-all:
    post:
      tags: [Authentication]
      operationId: logoutAll
      summary: Revogar todas as sessões
      responses:
        '204':
          description: Sessões revogadas.
        '401':
          $ref: '#/components/responses/Unauthorized'

  /auth/me:
    get:
      tags: [Authentication]
      operationId: getAuthenticatedUser
      summary: Consultar identidade autenticada
      responses:
        '200':
          description: Identidade autenticada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuthenticatedUserResponse'
        '401':
          $ref: '#/components/responses/Unauthorized'

  /auth/sessions:
    get:
      tags: [Authentication]
      operationId: listSessions
      summary: Listar sessões do usuário
      responses:
        '200':
          description: Sessões do usuário.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/SessionListResponse'
        '401':
          $ref: '#/components/responses/Unauthorized'

  /auth/sessions/{sessionId}:
    delete:
      tags: [Authentication]
      operationId: revokeSession
      summary: Revogar sessão
      parameters:
        - $ref: '#/components/parameters/SessionId'
      responses:
        '204':
          description: Sessão revogada.
        '404':
          $ref: '#/components/responses/NotFound'

  /auth/reauthentication:
    post:
      tags: [Authentication]
      operationId: reauthenticate
      summary: Solicitar reautenticação contextual
      description: |
        Emite credencial temporária vinculada ao usuário, à sessão e
        à ação solicitada. Validade inicial de cinco minutos.
        Operações críticas exigem uso único.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ReauthenticationRequest'
            example:
              password: "SenhaSegura123!"
              totpCode: "123456"
              action: ADMIN_TRANSACTION_REVERSAL
      responses:
        '200':
          description: Reautenticação concluída.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ReauthenticationResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'
        '429':
          $ref: '#/components/responses/TooManyRequests'

  /auth/2fa/setup:
    post:
      tags: [Authentication]
      operationId: setupTotp
      summary: Iniciar configuração TOTP
      responses:
        '200':
          description: Dados temporários para configuração.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TotpSetupResponse'
        '401':
          $ref: '#/components/responses/Unauthorized'

  /auth/2fa/confirm:
    post:
      tags: [Authentication]
      operationId: confirmTotp
      summary: Confirmar configuração TOTP
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TotpVerificationRequest'
      responses:
        '200':
          description: TOTP habilitado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/RecoveryCodesResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'

  /auth/2fa/verify:
    post:
      tags: [Authentication]
      operationId: verifyTotp
      summary: Validar código TOTP
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TotpVerificationRequest'
      responses:
        '204':
          description: Código validado.
        '401':
          $ref: '#/components/responses/Unauthorized'
        '429':
          $ref: '#/components/responses/TooManyRequests'

  /auth/2fa/disable:
    post:
      tags: [Authentication]
      operationId: disableTotp
      summary: Desativar TOTP quando permitido
      parameters:
        - $ref: '#/components/parameters/ReauthenticationToken'
      responses:
        '204':
          description: TOTP desativado.
        '403':
          $ref: '#/components/responses/Forbidden'
        '401':
          $ref: '#/components/responses/Unauthorized'

  /auth/2fa/recovery-codes:
    post:
      tags: [Authentication]
      operationId: regenerateRecoveryCodes
      summary: Gerar novos códigos de recuperação
      parameters:
        - $ref: '#/components/parameters/ReauthenticationToken'
      responses:
        '200':
          description: Novos códigos gerados. Os códigos anteriores são invalidados.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/RecoveryCodesResponse'
        '401':
          $ref: '#/components/responses/Unauthorized'

  /auth/2fa/recovery:
    post:
      tags: [Authentication]
      operationId: useRecoveryCode
      summary: Utilizar código de recuperação
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/RecoveryCodeRequest'
      responses:
        '204':
          description: Código de recuperação aceito.
        '401':
          $ref: '#/components/responses/Unauthorized'

  /auth/password/forgot:
    post:
      tags: [Authentication]
      operationId: requestPasswordRecovery
      summary: Solicitar recuperação de senha
      security: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/PasswordRecoveryRequest'
      responses:
        '202':
          description: Solicitação recebida, sem revelar a existência da conta.
        '400':
          $ref: '#/components/responses/BadRequest'
        '429':
          $ref: '#/components/responses/TooManyRequests'

  /auth/password/reset:
    post:
      tags: [Authentication]
      operationId: resetPassword
      summary: Redefinir senha
      security: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/PasswordResetRequest'
      responses:
        '204':
          description: Senha redefinida.
        '400':
          $ref: '#/components/responses/BadRequest'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /auth/email/verify:
    post:
      tags: [Authentication]
      operationId: verifyEmail
      summary: Verificar e-mail
      security: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/EmailVerificationRequest'
      responses:
        '204':
          description: E-mail verificado.
        '400':
          $ref: '#/components/responses/BadRequest'

  /auth/email/resend-verification:
    post:
      tags: [Authentication]
      operationId: resendEmailVerification
      summary: Reenviar verificação de e-mail
      responses:
        '202':
          description: Solicitação recebida.
        '429':
          $ref: '#/components/responses/TooManyRequests'

  # ==========================================================
  # CUSTOMERS
  # ==========================================================

  /customers/me:
    get:
      tags: [Customers]
      operationId: getMyCustomerProfile
      summary: Consultar perfil do cliente
      responses:
        '200':
          description: Perfil do cliente.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CustomerResponse'
        '401':
          $ref: '#/components/responses/Unauthorized'
    patch:
      tags: [Customers]
      operationId: updateMyCustomerProfile
      summary: Atualizar dados permitidos do perfil
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CustomerUpdateRequest'
      responses:
        '200':
          description: Perfil atualizado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CustomerResponse'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /customers/me/accounts:
    get:
      tags: [Customers]
      operationId: listMyAccounts
      summary: Listar contas vinculadas
      responses:
        '200':
          description: Contas acessíveis.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountListResponse'

  /customers/me/loans:
    get:
      tags: [Customers]
      operationId: listMyLoans
      summary: Listar empréstimos do cliente
      responses:
        '200':
          description: Empréstimos.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoanListResponse'

  /customers/me/cards:
    get:
      tags: [Customers]
      operationId: listMyCards
      summary: Listar cartões do cliente
      responses:
        '200':
          description: Cartões.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CardListResponse'

  /customers/me/notifications:
    get:
      tags: [Customers]
      operationId: listMyNotifications
      summary: Listar notificações do cliente
      responses:
        '200':
          description: Notificações.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotificationListResponse'

  # ==========================================================
  # ACCOUNTS
  # ==========================================================

  /accounts:
    get:
      tags: [Accounts]
      operationId: listAccounts
      summary: Listar contas acessíveis
      parameters:
        - $ref: '#/components/parameters/Page'
        - $ref: '#/components/parameters/Size'
        - $ref: '#/components/parameters/Sort'
      responses:
        '200':
          description: Contas.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountListResponse'
    post:
      tags: [Accounts]
      operationId: createAccount
      summary: Solicitar abertura de conta
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AccountCreateRequest'
      responses:
        '201':
          description: Solicitação de abertura registrada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountResponse'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /accounts/{accountId}:
    parameters:
      - $ref: '#/components/parameters/AccountId'
    get:
      tags: [Accounts]
      operationId: getAccount
      summary: Consultar conta
      responses:
        '200':
          description: Conta.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  /accounts/{accountId}/balance:
    get:
      tags: [Accounts]
      operationId: getAccountBalance
      summary: Consultar saldo
      parameters:
        - $ref: '#/components/parameters/AccountId'
      responses:
        '200':
          description: Saldo contabilizado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/BalanceResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  /accounts/{accountId}/statement:
    get:
      tags: [Accounts]
      operationId: getAccountStatement
      summary: Consultar extrato
      parameters:
        - $ref: '#/components/parameters/AccountId'
        - name: startDate
          in: query
          schema:
            type: string
            format: date
        - name: endDate
          in: query
          schema:
            type: string
            format: date
        - name: type
          in: query
          schema:
            type: string
        - name: status
          in: query
          schema:
            $ref: '#/components/schemas/TransactionStatus'
        - name: minAmount
          in: query
          schema:
            $ref: '#/components/schemas/Money'
        - name: maxAmount
          in: query
          schema:
            $ref: '#/components/schemas/Money'
        - name: description
          in: query
          schema:
            type: string
            maxLength: 200
        - $ref: '#/components/parameters/Page'
        - name: size
          in: query
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
        - name: sort
          in: query
          schema:
            type: string
            default: createdAt,desc
            enum:
              - createdAt,desc
              - createdAt,asc
              - amount,desc
              - amount,asc
      responses:
        '200':
          description: Extrato paginado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/StatementResponse'
        '400':
          $ref: '#/components/responses/BadRequest'
        '403':
          $ref: '#/components/responses/Forbidden'

  /accounts/{accountId}/block:
    post:
      tags: [Accounts]
      operationId: blockAccount
      summary: Bloquear conta
      parameters:
        - $ref: '#/components/parameters/AccountId'
      responses:
        '200':
          description: Conta bloqueada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /accounts/{accountId}/unblock:
    post:
      tags: [Accounts]
      operationId: unblockAccount
      summary: Desbloquear conta
      parameters:
        - $ref: '#/components/parameters/AccountId'
      responses:
        '200':
          description: Conta desbloqueada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /accounts/{accountId}/close:
    post:
      tags: [Accounts]
      operationId: closeAccount
      summary: Solicitar encerramento de conta
      parameters:
        - $ref: '#/components/parameters/AccountId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      responses:
        '200':
          description: Encerramento processado ou estado atualizado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountResponse'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /accounts/{accountId}/holders:
    get:
      tags: [Accounts]
      operationId: listAccountHolders
      summary: Listar titulares
      parameters:
        - $ref: '#/components/parameters/AccountId'
      responses:
        '200':
          description: Titulares ativos e histórico permitido.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountHolderListResponse'

  /accounts/{accountId}/invitations:
    get:
      tags: [Account Invitations]
      operationId: listAccountInvitations
      summary: Listar convites da conta
      parameters:
        - $ref: '#/components/parameters/AccountId'
      responses:
        '200':
          description: Convites.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/InvitationListResponse'
    post:
      tags: [Account Invitations]
      operationId: inviteAccountHolder
      summary: Convidar titular
      parameters:
        - $ref: '#/components/parameters/AccountId'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AccountInvitationRequest'
      responses:
        '201':
          description: Convite criado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/InvitationResponse'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /account-invitations/{invitationId}/accept:
    post:
      tags: [Account Invitations]
      operationId: acceptAccountInvitation
      summary: Aceitar convite de conta
      parameters:
        - $ref: '#/components/parameters/InvitationId'
      responses:
        '200':
          description: Convite aceito.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/InvitationResponse'
        '403':
          $ref: '#/components/responses/Forbidden'
        '409':
          $ref: '#/components/responses/Conflict'

  /account-invitations/{invitationId}/cancel:
    post:
      tags: [Account Invitations]
      operationId: cancelAccountInvitation
      summary: Cancelar convite
      parameters:
        - $ref: '#/components/parameters/InvitationId'
      responses:
        '200':
          description: Convite cancelado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/InvitationResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /accounts/{accountId}/leave:
    post:
      tags: [Accounts]
      operationId: leaveAccount
      summary: Solicitar saída voluntária
      parameters:
        - $ref: '#/components/parameters/AccountId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      responses:
        '200':
          description: Saída processada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountHolderResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /accounts/{accountId}/closure-consents:
    post:
      tags: [Accounts]
      operationId: registerAccountClosureConsent
      summary: Registrar consentimento de encerramento conjunto
      parameters:
        - $ref: '#/components/parameters/AccountId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      responses:
        '200':
          description: Consentimento registrado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ClosureConsentResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  # ==========================================================
  # FINANCIAL TRANSACTIONS
  # ==========================================================

  /transfers:
    post:
      tags: [Transfers]
      operationId: createTransfer
      summary: Realizar transferência interna
      parameters:
        - $ref: '#/components/parameters/IdempotencyKey'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TransferRequest'
            example:
              sourceAccountId: 4b8d6b6d-3e0e-4f9e-8e7e-8f2a4e9a1234
              destinationAccountId: 7c8d6b6d-3e0e-4f9e-8e7e-8f2a4e9a5678
              amount: "250.00"
              description: Transferência entre contas
      responses:
        '201':
          description: Transferência contabilizada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TransactionResponse'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /transfers/{transferId}:
    get:
      tags: [Transfers]
      operationId: getTransfer
      summary: Consultar transferência
      parameters:
        - $ref: '#/components/parameters/TransferId'
      responses:
        '200':
          description: Transferência.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TransactionResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  /accounts/{accountId}/transfers:
    get:
      tags: [Transfers]
      operationId: listAccountTransfers
      summary: Listar transferências da conta
      parameters:
        - $ref: '#/components/parameters/AccountId'
        - $ref: '#/components/parameters/Page'
        - $ref: '#/components/parameters/Size'
      responses:
        '200':
          description: Transferências.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TransactionListResponse'

  /deposits:
    post:
      tags: [Deposits]
      operationId: createDeposit
      summary: Registrar depósito simulado
      parameters:
        - $ref: '#/components/parameters/IdempotencyKey'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AccountAmountRequest'
      responses:
        '201':
          description: Depósito contabilizado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TransactionResponse'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /deposits/{depositId}:
    get:
      tags: [Deposits]
      operationId: getDeposit
      summary: Consultar depósito
      parameters:
        - $ref: '#/components/parameters/DepositId'
      responses:
        '200':
          description: Depósito.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TransactionResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  /accounts/{accountId}/deposits:
    get:
      tags: [Deposits]
      operationId: listDeposits
      summary: Listar depósitos
      parameters:
        - $ref: '#/components/parameters/AccountId'
        - $ref: '#/components/parameters/Page'
        - $ref: '#/components/parameters/Size'
      responses:
        '200':
          description: Depósitos.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TransactionListResponse'

  /withdrawals:
    post:
      tags: [Withdrawals]
      operationId: createWithdrawal
      summary: Registrar saque simulado
      parameters:
        - $ref: '#/components/parameters/IdempotencyKey'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AccountAmountRequest'
      responses:
        '201':
          description: Saque contabilizado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TransactionResponse'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /withdrawals/{withdrawalId}:
    get:
      tags: [Withdrawals]
      operationId: getWithdrawal
      summary: Consultar saque
      parameters:
        - $ref: '#/components/parameters/WithdrawalId'
      responses:
        '200':
          description: Saque.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TransactionResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  /accounts/{accountId}/withdrawals:
    get:
      tags: [Withdrawals]
      operationId: listWithdrawals
      summary: Listar saques
      parameters:
        - $ref: '#/components/parameters/AccountId'
        - $ref: '#/components/parameters/Page'
        - $ref: '#/components/parameters/Size'
      responses:
        '200':
          description: Saques.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TransactionListResponse'

  /transactions/{transactionId}:
    get:
      tags: [Transactions]
      operationId: getTransaction
      summary: Consultar transação
      parameters:
        - $ref: '#/components/parameters/TransactionId'
      responses:
        '200':
          description: Transação.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TransactionResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  # ==========================================================
  # PAYMENTS
  # ==========================================================

  /payment-providers:
    get:
      tags: [Payments]
      operationId: listPaymentProviders
      summary: Listar prestadores simulados
      responses:
        '200':
          description: Prestadores disponíveis.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PaymentProviderListResponse'

  /payments:
    post:
      tags: [Payments]
      operationId: createPayment
      summary: Criar pagamento simulado
      parameters:
        - $ref: '#/components/parameters/IdempotencyKey'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/PaymentCreateRequest'
      responses:
        '201':
          description: Pagamento imediato concluído ou agendamento criado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PaymentResponse'
        '202':
          description: Processamento efetivamente pendente.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PaymentResponse'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /payments/{paymentId}:
    get:
      tags: [Payments]
      operationId: getPayment
      summary: Consultar pagamento
      parameters:
        - $ref: '#/components/parameters/PaymentId'
      responses:
        '200':
          description: Pagamento.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PaymentResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  /payments/{paymentId}/cancel:
    post:
      tags: [Payments]
      operationId: cancelPayment
      summary: Cancelar pagamento elegível
      parameters:
        - $ref: '#/components/parameters/PaymentId'
      responses:
        '200':
          description: Pagamento cancelado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PaymentResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /accounts/{accountId}/payments:
    get:
      tags: [Payments]
      operationId: listAccountPayments
      summary: Listar pagamentos
      parameters:
        - $ref: '#/components/parameters/AccountId'
        - $ref: '#/components/parameters/Page'
        - $ref: '#/components/parameters/Size'
      responses:
        '200':
          description: Pagamentos.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PaymentListResponse'

  # ==========================================================
  # LOANS
  # ==========================================================

  /loans/simulations:
    post:
      tags: [Loans]
      operationId: simulateLoan
      summary: Simular empréstimo
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/LoanSimulationRequest'
      responses:
        '200':
          description: Simulação calculada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoanSimulationResponse'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /loans:
    get:
      tags: [Loans]
      operationId: listLoans
      summary: Listar empréstimos do cliente
      parameters:
        - $ref: '#/components/parameters/Page'
        - $ref: '#/components/parameters/Size'
      responses:
        '200':
          description: Empréstimos.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoanListResponse'
    post:
      tags: [Loans]
      operationId: requestLoan
      summary: Solicitar empréstimo
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/LoanRequest'
      responses:
        '201':
          description: Solicitação registrada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoanResponse'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /loans/{loanId}:
    get:
      tags: [Loans]
      operationId: getLoan
      summary: Consultar empréstimo
      parameters:
        - $ref: '#/components/parameters/LoanId'
      responses:
        '200':
          description: Empréstimo.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoanResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  /loans/{loanId}/installments:
    get:
      tags: [Loans]
      operationId: listLoanInstallments
      summary: Listar parcelas
      parameters:
        - $ref: '#/components/parameters/LoanId'
      responses:
        '200':
          description: Parcelas.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/InstallmentListResponse'

  /loans/{loanId}/installments/{installmentId}:
    get:
      tags: [Loans]
      operationId: getLoanInstallment
      summary: Consultar parcela
      parameters:
        - $ref: '#/components/parameters/LoanId'
        - $ref: '#/components/parameters/InstallmentId'
      responses:
        '200':
          description: Parcela.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/InstallmentResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  /loans/{loanId}/installments/{installmentId}/payments:
    get:
      tags: [Loans]
      operationId: listInstallmentPayments
      summary: Consultar pagamentos da parcela
      parameters:
        - $ref: '#/components/parameters/LoanId'
        - $ref: '#/components/parameters/InstallmentId'
      responses:
        '200':
          description: Pagamentos da parcela.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/InstallmentPaymentListResponse'
    post:
      tags: [Loans]
      operationId: payInstallment
      summary: Quitar integralmente parcela
      description: |
        Pagamento exclusivamente integral na primeira versão.
        O backend calcula o valor devido. Não aceitar valor informado
        pelo cliente. A liquidação é simulada e não depende de conta
        bancária ativa.
      parameters:
        - $ref: '#/components/parameters/LoanId'
        - $ref: '#/components/parameters/InstallmentId'
        - $ref: '#/components/parameters/IdempotencyKey'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/InstallmentPaymentRequest'
            example:
              paymentMethod: SIMULATED_SETTLEMENT
      responses:
        '201':
          description: Parcela quitada e contabilizada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/InstallmentPaymentResponse'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /loans/{loanId}/contract:
    get:
      tags: [Loans]
      operationId: getLoanContract
      summary: Consultar contrato do empréstimo
      parameters:
        - $ref: '#/components/parameters/LoanId'
      responses:
        '200':
          description: Condições contratuais preservadas.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoanContractResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  /loans/{loanId}/cancel:
    post:
      tags: [Loans]
      operationId: cancelLoanRequest
      summary: Cancelar solicitação elegível
      parameters:
        - $ref: '#/components/parameters/LoanId'
      responses:
        '200':
          description: Solicitação cancelada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoanResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  # ==========================================================
  # CARDS
  # ==========================================================

  /cards:
    get:
      tags: [Cards]
      operationId: listCards
      summary: Listar cartões virtuais
      responses:
        '200':
          description: Cartões.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CardListResponse'
    post:
      tags: [Cards]
      operationId: createCard
      summary: Criar cartão virtual
      parameters:
        - $ref: '#/components/parameters/IdempotencyKey'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CardCreateRequest'
      responses:
        '201':
          description: Cartão criado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CardResponse'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /cards/{cardId}:
    get:
      tags: [Cards]
      operationId: getCard
      summary: Consultar cartão
      parameters:
        - $ref: '#/components/parameters/CardId'
      responses:
        '200':
          description: Cartão.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CardResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  /cards/{cardId}/block:
    post:
      tags: [Cards]
      operationId: blockCard
      summary: Bloquear cartão
      parameters:
        - $ref: '#/components/parameters/CardId'
      responses:
        '200':
          description: Cartão bloqueado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CardResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /cards/{cardId}/unblock:
    post:
      tags: [Cards]
      operationId: unblockCard
      summary: Desbloquear cartão
      parameters:
        - $ref: '#/components/parameters/CardId'
      responses:
        '200':
          description: Cartão desbloqueado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CardResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /cards/{cardId}/cancel:
    post:
      tags: [Cards]
      operationId: cancelCard
      summary: Cancelar cartão
      parameters:
        - $ref: '#/components/parameters/CardId'
      responses:
        '200':
          description: Cartão cancelado definitivamente.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CardResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /cards/{cardId}/purchases:
    post:
      tags: [Cards]
      operationId: createCardPurchase
      summary: Registrar compra simulada
      parameters:
        - $ref: '#/components/parameters/CardId'
        - $ref: '#/components/parameters/IdempotencyKey'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CardPurchaseRequest'
      responses:
        '201':
          description: Compra simulada contabilizada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CardTransactionResponse'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /cards/{cardId}/transactions:
    get:
      tags: [Cards]
      operationId: listCardTransactions
      summary: Consultar compras do cartão
      parameters:
        - $ref: '#/components/parameters/CardId'
        - $ref: '#/components/parameters/Page'
        - $ref: '#/components/parameters/Size'
      responses:
        '200':
          description: Compras.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CardTransactionListResponse'

  /card-transactions/{transactionId}:
    get:
      tags: [Cards]
      operationId: getCardTransaction
      summary: Consultar compra simulada
      parameters:
        - $ref: '#/components/parameters/TransactionId'
      responses:
        '200':
          description: Compra.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CardTransactionResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  /card-transactions/{transactionId}/reverse:
    post:
      tags: [Cards]
      operationId: reverseCardTransaction
      summary: Solicitar estorno elegível de compra simulada
      description: |
        Fluxo específico de compra com cartão. Deve aplicar a mesma
        integridade contábil do estorno administrativo e restaurar
        o limite consumido conforme as regras do cartão.
      parameters:
        - $ref: '#/components/parameters/TransactionId'
        - $ref: '#/components/parameters/IdempotencyKey'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CardReversalRequest'
      responses:
        '201':
          description: Compensação registrada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TransactionResponse'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  # ==========================================================
  # ADMINISTRATION
  # ==========================================================

  /admin/loans:
    get:
      tags: [Administration]
      operationId: adminListLoans
      summary: Listar solicitações de empréstimo
      security:
        - bearerAuth: []
          reauthenticationToken: []
      parameters:
        - $ref: '#/components/parameters/Page'
        - $ref: '#/components/parameters/Size'
      responses:
        '200':
          description: Solicitações.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoanListResponse'
        '403':
          $ref: '#/components/responses/Forbidden'

  /admin/loans/{loanId}/review:
    post:
      tags: [Administration]
      operationId: reviewLoan
      summary: Iniciar análise de empréstimo
      parameters:
        - $ref: '#/components/parameters/LoanId'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AdminReasonRequest'
      responses:
        '200':
          description: Análise iniciada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoanResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /admin/loans/{loanId}/approve:
    post:
      tags: [Administration]
      operationId: approveLoan
      summary: Aprovar empréstimo
      parameters:
        - $ref: '#/components/parameters/LoanId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AdminReasonRequest'
      responses:
        '200':
          description: Empréstimo aprovado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoanResponse'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /admin/loans/{loanId}/reject:
    post:
      tags: [Administration]
      operationId: rejectLoan
      summary: Rejeitar empréstimo
      parameters:
        - $ref: '#/components/parameters/LoanId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AdminReasonRequest'
      responses:
        '200':
          description: Empréstimo rejeitado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoanResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /admin/loans/{loanId}/contract:
    post:
      tags: [Administration]
      operationId: contractLoan
      summary: Formalizar contratação aprovada
      parameters:
        - $ref: '#/components/parameters/LoanId'
        - $ref: '#/components/parameters/IdempotencyKey'
      responses:
        '201':
          description: Contrato formalizado e operação de desembolso processada conforme regra de negócio.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/LoanContractResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /admin/transactions/{transactionId}/reversals:
    post:
      tags: [Administration]
      operationId: createAdministrativeReversal
      summary: Criar estorno administrativo integral
      description: |
        Operação exclusiva de ADMIN. Exige 2FA, reautenticação,
        justificativa, confirmação, idempotência e auditoria.
        Não permite estornar empréstimos, parcelas, estornos ou
        transações que não estejam COMPLETED.
      parameters:
        - $ref: '#/components/parameters/TransactionId'
        - $ref: '#/components/parameters/IdempotencyKey'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ReversalRequest'
            example:
              reason: Correção de operação processada indevidamente
              confirmation: true
      responses:
        '201':
          description: Estorno integral contabilizado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ReversalResponse'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /admin/outbox-events/{eventId}/reprocess:
    post:
      tags: [Administration]
      operationId: reprocessOutboxEvent
      summary: Solicitar reprocessamento administrativo de evento da Outbox
      description: |
        Operação exclusiva de ADMIN. Exige 2FA, reautenticação vinculada à ação,
        justificativa, confirmação, idempotência e auditoria. Preserva o payload
        original e todas as tentativas anteriores. A solicitação é elegível para
        estado terminal FAILED conforme a decisão complementar v1.1; há divergência
        documental pendente com DEAD_LETTER na revisão da modelagem de dados.
      parameters:
        - $ref: '#/components/parameters/EventId'
        - $ref: '#/components/parameters/IdempotencyKey'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/OutboxReprocessRequest'
            example:
              reason: Reprocessamento após correção da causa operacional
              confirmation: true
      responses:
        '202':
          description: Solicitação aceita para processamento assíncrono.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/OutboxReprocessAcceptedResponse'
              example:
                eventId: 4b8d6b6d-3e0e-4f9e-8e7e-8f2a4e9a1234
                status: PENDING
                message: Reprocessamento solicitado.
                statusUrl: /api/v1/admin/outbox-events/4b8d6b6d-3e0e-4f9e-8e7e-8f2a4e9a1234
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'
        '404':
          $ref: '#/components/responses/NotFound'
        '409':
          $ref: '#/components/responses/Conflict'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /admin/outbox-events/{eventId}:
    get:
      tags: [Administration]
      operationId: getOutboxEventStatus
      summary: Consultar estado e tentativas de evento da Outbox
      description: Consulta administrativa para acompanhamento por `statusUrl`.
      parameters:
        - $ref: '#/components/parameters/EventId'
      responses:
        '200':
          description: Estado atual e histórico de tentativas permitido para consulta.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/OutboxEventResponse'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '403':
          $ref: '#/components/responses/Forbidden'
        '404':
          $ref: '#/components/responses/NotFound'

  /admin/customers:
    get:
      tags: [Administration]
      operationId: adminListCustomers
      summary: Listar clientes
      parameters:
        - $ref: '#/components/parameters/Page'
        - $ref: '#/components/parameters/Size'
      responses:
        '200':
          description: Clientes.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CustomerListResponse'
        '403':
          $ref: '#/components/responses/Forbidden'

  /admin/customers/{customerId}:
    get:
      tags: [Administration]
      operationId: adminGetCustomer
      summary: Consultar cliente
      parameters:
        - $ref: '#/components/parameters/CustomerId'
      responses:
        '200':
          description: Cliente.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CustomerResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  /admin/customers/{customerId}/block:
    post:
      tags: [Administration]
      operationId: adminBlockCustomer
      summary: Bloquear cliente
      parameters:
        - $ref: '#/components/parameters/CustomerId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AdminReasonRequest'
      responses:
        '200':
          description: Cliente bloqueado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CustomerResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /admin/customers/{customerId}/unblock:
    post:
      tags: [Administration]
      operationId: adminUnblockCustomer
      summary: Desbloquear cliente
      parameters:
        - $ref: '#/components/parameters/CustomerId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AdminReasonRequest'
      responses:
        '200':
          description: Cliente desbloqueado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CustomerResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /admin/customers/{customerId}/suspend:
    post:
      tags: [Administration]
      operationId: adminSuspendCustomer
      summary: Suspender cliente
      parameters:
        - $ref: '#/components/parameters/CustomerId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AdminReasonRequest'
      responses:
        '200':
          description: Cliente suspenso.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CustomerResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /admin/customers/{customerId}/close:
    post:
      tags: [Administration]
      operationId: adminCloseCustomer
      summary: Encerrar cliente
      parameters:
        - $ref: '#/components/parameters/CustomerId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AdminReasonRequest'
      responses:
        '200':
          description: Cliente encerrado conforme regras de negócio.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CustomerResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /admin/accounts:
    get:
      tags: [Administration]
      operationId: adminListAccounts
      summary: Listar contas
      parameters:
        - $ref: '#/components/parameters/Page'
        - $ref: '#/components/parameters/Size'
      responses:
        '200':
          description: Contas.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountListResponse'

  /admin/accounts/{accountId}:
    get:
      tags: [Administration]
      operationId: adminGetAccount
      summary: Consultar conta
      parameters:
        - $ref: '#/components/parameters/AccountId'
      responses:
        '200':
          description: Conta.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  /admin/accounts/{accountId}/block:
    post:
      tags: [Administration]
      operationId: adminBlockAccount
      summary: Bloquear conta administrativamente
      parameters:
        - $ref: '#/components/parameters/AccountId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AdminReasonRequest'
      responses:
        '200':
          description: Conta bloqueada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountResponse'

  /admin/accounts/{accountId}/unblock:
    post:
      tags: [Administration]
      operationId: adminUnblockAccount
      summary: Desbloquear conta administrativamente
      parameters:
        - $ref: '#/components/parameters/AccountId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AdminReasonRequest'
      responses:
        '200':
          description: Conta desbloqueada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountResponse'

  /admin/accounts/{accountId}/suspend:
    post:
      tags: [Administration]
      operationId: adminSuspendAccount
      summary: Suspender conta administrativamente
      parameters:
        - $ref: '#/components/parameters/AccountId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AdminReasonRequest'
      responses:
        '200':
          description: Conta suspensa.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountResponse'

  /admin/accounts/{accountId}/close:
    post:
      tags: [Administration]
      operationId: adminCloseAccount
      summary: Encerrar conta administrativamente
      parameters:
        - $ref: '#/components/parameters/AccountId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AdminReasonRequest'
      responses:
        '200':
          description: Conta encerrada conforme regras de negócio.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AccountResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /admin/administrators:
    get:
      tags: [Administration]
      operationId: listAdministrators
      summary: Listar administradores
      responses:
        '200':
          description: Administradores.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AdministratorListResponse'

  /admin/administrators/invitations:
    get:
      tags: [Administration]
      operationId: listAdministratorInvitations
      summary: Listar convites administrativos
      responses:
        '200':
          description: Convites.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/InvitationListResponse'
    post:
      tags: [Administration]
      operationId: inviteAdministrator
      summary: Convidar administrador
      parameters:
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AdminInvitationRequest'
      responses:
        '201':
          description: Convite criado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/InvitationResponse'
        '409':
          $ref: '#/components/responses/Conflict'

  /admin/administrators/invitations/{invitationId}/cancel:
    post:
      tags: [Administration]
      operationId: cancelAdministratorInvitation
      summary: Cancelar convite administrativo
      parameters:
        - $ref: '#/components/parameters/InvitationId'
      responses:
        '200':
          description: Convite cancelado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/InvitationResponse'

  /admin/administrators/{userId}/block:
    post:
      tags: [Administration]
      operationId: blockAdministrator
      summary: Bloquear administrador
      parameters:
        - $ref: '#/components/parameters/UserId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AdminReasonRequest'
      responses:
        '200':
          description: Administrador bloqueado.
        '409':
          $ref: '#/components/responses/Conflict'

  /admin/administrators/{userId}/unblock:
    post:
      tags: [Administration]
      operationId: unblockAdministrator
      summary: Reativar administrador
      parameters:
        - $ref: '#/components/parameters/UserId'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AdminReasonRequest'
      responses:
        '200':
          description: Administrador reativado.
        '409':
          $ref: '#/components/responses/Conflict'

  /admin/settings:
    get:
      tags: [Administration]
      operationId: listSystemSettings
      summary: Consultar configurações administrativas
      responses:
        '200':
          description: Configurações visíveis ao administrador.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/SettingsListResponse'

  /admin/settings/{key}:
    get:
      tags: [Administration]
      operationId: getSystemSetting
      summary: Consultar configuração
      parameters:
        - $ref: '#/components/parameters/SettingKey'
      responses:
        '200':
          description: Configuração.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/SettingResponse'
        '404':
          $ref: '#/components/responses/NotFound'
    patch:
      tags: [Administration]
      operationId: updateSystemSetting
      summary: Alterar configuração
      parameters:
        - $ref: '#/components/parameters/SettingKey'
        - $ref: '#/components/parameters/ReauthenticationToken'
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/SettingUpdateRequest'
      responses:
        '200':
          description: Configuração atualizada e auditada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/SettingResponse'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  # ==========================================================
  # NOTIFICATIONS
  # ==========================================================

  /notifications:
    get:
      tags: [Notifications]
      operationId: listNotifications
      summary: Listar notificações
      parameters:
        - $ref: '#/components/parameters/Page'
        - $ref: '#/components/parameters/Size'
      responses:
        '200':
          description: Notificações.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotificationListResponse'

  /notifications/{notificationId}:
    get:
      tags: [Notifications]
      operationId: getNotification
      summary: Consultar notificação
      parameters:
        - $ref: '#/components/parameters/NotificationId'
      responses:
        '200':
          description: Notificação.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotificationResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  /notifications/{notificationId}/read:
    patch:
      tags: [Notifications]
      operationId: markNotificationRead
      summary: Marcar notificação como lida
      parameters:
        - $ref: '#/components/parameters/NotificationId'
      responses:
        '204':
          description: Notificação marcada como lida.
        '404':
          $ref: '#/components/responses/NotFound'

  /notifications/read-all:
    post:
      tags: [Notifications]
      operationId: markAllNotificationsRead
      summary: Marcar todas as notificações como lidas
      responses:
        '204':
          description: Notificações atualizadas.

  /notifications/preferences:
    get:
      tags: [Notifications]
      operationId: getNotificationPreferences
      summary: Consultar preferências
      responses:
        '200':
          description: Preferências.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotificationPreferencesResponse'
    patch:
      tags: [Notifications]
      operationId: updateNotificationPreferences
      summary: Atualizar preferências
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/NotificationPreferencesUpdateRequest'
      responses:
        '200':
          description: Preferências atualizadas.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotificationPreferencesResponse'

  # ==========================================================
  # AUDIT
  # ==========================================================

  /admin/audit-events:
    get:
      tags: [Audit]
      operationId: listAuditEvents
      summary: Consultar eventos de auditoria
      parameters:
        - name: actorId
          in: query
          schema:
            type: string
            format: uuid
        - name: eventType
          in: query
          schema:
            type: string
        - name: resourceId
          in: query
          schema:
            type: string
        - name: startDate
          in: query
          schema:
            type: string
            format: date-time
        - name: endDate
          in: query
          schema:
            type: string
            format: date-time
        - name: result
          in: query
          schema:
            type: string
            enum: [SUCCESS, FAILURE]
        - $ref: '#/components/parameters/Page'
        - $ref: '#/components/parameters/Size'
      responses:
        '200':
          description: Eventos de auditoria.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuditEventListResponse'
        '403':
          $ref: '#/components/responses/Forbidden'

  /admin/audit-events/{eventId}:
    get:
      tags: [Audit]
      operationId: getAuditEvent
      summary: Consultar evento de auditoria
      parameters:
        - $ref: '#/components/parameters/EventId'
      responses:
        '200':
          description: Evento.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/AuditEventResponse'
        '404':
          $ref: '#/components/responses/NotFound'

  # ==========================================================
  # PRIVACY
  # ==========================================================

  /privacy/requests:
    get:
      tags: [Privacy]
      operationId: listPrivacyRequests
      summary: Listar solicitações de privacidade
      responses:
        '200':
          description: Solicitações do titular autenticado.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PrivacyRequestListResponse'
    post:
      tags: [Privacy]
      operationId: createPrivacyRequest
      summary: Registrar solicitação de privacidade
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/PrivacyRequestCreate'
      responses:
        '201':
          description: Solicitação registrada.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PrivacyRequestResponse'
        '422':
          $ref: '#/components/responses/BusinessRuleError'

  /privacy/requests/{requestId}:
    get:
      tags: [Privacy]
      operationId: getPrivacyRequest
      summary: Consultar solicitação de privacidade
      parameters:
        - $ref: '#/components/parameters/PrivacyRequestId'
      responses:
        '200':
          description: Solicitação.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/PrivacyRequestResponse'
        '404':
          $ref: '#/components/responses/NotFound'

components:

  # ==========================================================
  # SECURITY SCHEMES
  # ==========================================================

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
      description: Access Token JWT com validade inicial de dez minutos.

    refreshCookie:
      type: apiKey
      in: cookie
      name: MYBANK_REFRESH
      description: |
        Refresh Token em cookie HttpOnly, Secure e SameSite.
        Utilizado exclusivamente nos endpoints de renovação e encerramento
        de sessão.

    reauthenticationToken:
      type: apiKey
      in: header
      name: X-Reauthentication-Token
      description: |
        Token temporário vinculado ao usuário, sessão e ação.
        Validade inicial de cinco minutos. Uso único em operações críticas.

  # ==========================================================
  # PARAMETERS
  # ==========================================================

  parameters:
    AccountId:
      name: accountId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    CustomerId:
      name: customerId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    UserId:
      name: userId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    SessionId:
      name: sessionId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    ChallengeId:
      name: challengeId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    InvitationId:
      name: invitationId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    TransferId:
      name: transferId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    DepositId:
      name: depositId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    WithdrawalId:
      name: withdrawalId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    TransactionId:
      name: transactionId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    PaymentId:
      name: paymentId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    LoanId:
      name: loanId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    InstallmentId:
      name: installmentId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    CardId:
      name: cardId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    NotificationId:
      name: notificationId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    EventId:
      name: eventId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    PrivacyRequestId:
      name: requestId
      in: path
      required: true
      schema:
        type: string
        format: uuid

    SettingKey:
      name: key
      in: path
      required: true
      schema:
        type: string
        minLength: 1
        maxLength: 120

    Page:
      name: page
      in: query
      schema:
        type: integer
        minimum: 0
        default: 0

    Size:
      name: size
      in: query
      schema:
        type: integer
        minimum: 1
        maximum: 100
        default: 20

    Sort:
      name: sort
      in: query
      schema:
        type: string
        default: createdAt,desc
        maxLength: 100

    IdempotencyKey:
      name: Idempotency-Key
      in: header
      required: true
      description: Chave única para impedir execução duplicada.
      schema:
        type: string
        minLength: 16
        maxLength: 128

    ReauthenticationToken:
      name: X-Reauthentication-Token
      in: header
      required: true
      description: Credencial temporária vinculada à ação crítica.
      schema:
        type: string
        minLength: 20
        maxLength: 2048

  # ==========================================================
  # COMMON RESPONSES
  # ==========================================================

  responses:
    BadRequest:
      description: Requisição inválida.
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/Problem'
          example:
            type: https://mybank.example/errors/validation
            title: Requisição inválida
            status: 400
            detail: Um ou mais campos possuem formato inválido.
            code: VALIDATION_ERROR
            correlationId: 4b8d6b6d-3e0e-4f9e-8e7e-8f2a4e9a1234
            timestamp: '2026-11-12T14:30:00-03:00'
            errors:
              - field: amount
                code: INVALID_FORMAT
                message: Informe um valor decimal válido.

    Unauthorized:
      description: Autenticação inválida, ausente ou expirada.
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/Problem'
          example:
            type: https://mybank.example/errors/unauthorized
            title: Não autenticado
            status: 401
            detail: A autenticação não pôde ser validada.
            code: UNAUTHENTICATED
            correlationId: 4b8d6b6d-3e0e-4f9e-8e7e-8f2a4e9a1234
            timestamp: '2026-11-12T14:30:00-03:00'

    Forbidden:
      description: Usuário autenticado sem permissão.
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/Problem'

    NotFound:
      description: Recurso inexistente ou não acessível.
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/Problem'

    Conflict:
      description: Conflito de estado, idempotência ou concorrência.
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/Problem'
          example:
            type: https://mybank.example/errors/conflict
            title: Conflito
            status: 409
            detail: A operação não pode ser executada no estado atual.
            code: RESOURCE_STATE_CONFLICT
            correlationId: 4b8d6b6d-3e0e-4f9e-8e7e-8f2a4e9a1234
            timestamp: '2026-11-12T14:30:00-03:00'

    BusinessRuleError:
      description: Regra de negócio não atendida.
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/Problem'
          example:
            type: https://mybank.example/errors/business-rule
            title: Regra de negócio não atendida
            status: 422
            detail: Saldo insuficiente para realizar a operação.
            code: INSUFFICIENT_BALANCE
            correlationId: 4b8d6b6d-3e0e-4f9e-8e7e-8f2a4e9a1234
            timestamp: '2026-11-12T14:30:00-03:00'

    TooManyRequests:
      description: Limite de requisições excedido.
      headers:
        Retry-After:
          schema:
            type: integer
          description: Tempo sugerido para nova tentativa, em segundos.
      content:
        application/problem+json:
          schema:
            $ref: '#/components/schemas/Problem'

  # ==========================================================
  # SCHEMAS
  # ==========================================================

  schemas:

    # ----- Primitives and common structures -----

    Money:
      type: string
      pattern: '^(0|[1-9][0-9]{0,16})\.[0-9]{2}$'
      description: Valor monetário decimal em BRL, com duas casas decimais.
      examples: ['1250.75', '0.00']

    Currency:
      type: string
      const: BRL

    UUID:
      type: string
      format: uuid

    DateTime:
      type: string
      format: date-time

    PageMeta:
      type: object
      additionalProperties: false
      required: [page, size, totalElements, totalPages, correlationId]
      properties:
        page:
          type: integer
          minimum: 0
        size:
          type: integer
          minimum: 1
          maximum: 100
        totalElements:
          type: integer
          minimum: 0
        totalPages:
          type: integer
          minimum: 0
        correlationId:
          $ref: '#/components/schemas/UUID'

    ResponseMeta:
      type: object
      additionalProperties: false
      required: [correlationId, timestamp]
      properties:
        correlationId:
          $ref: '#/components/schemas/UUID'
        timestamp:
          $ref: '#/components/schemas/DateTime'

    ProblemField:
      type: object
      additionalProperties: false
      required: [field, code, message]
      properties:
        field:
          type: string
        code:
          type: string
        message:
          type: string

    Problem:
      type: object
      additionalProperties: false
      required: [type, title, status, code, correlationId, timestamp]
      properties:
        type:
          type: string
          format: uri
        title:
          type: string
        status:
          type: integer
        detail:
          type: string
        instance:
          type: string
        code:
          $ref: '#/components/schemas/ErrorCode'
        correlationId:
          $ref: '#/components/schemas/UUID'
        timestamp:
          $ref: '#/components/schemas/DateTime'
        errors:
          type: array
          items:
            $ref: '#/components/schemas/ProblemField'

    ErrorCode:
      type: string
      enum:
        - VALIDATION_ERROR
        - INVALID_FORMAT
        - UNAUTHENTICATED
        - INVALID_CREDENTIALS
        - TOKEN_EXPIRED
        - TOKEN_REVOKED
        - TOKEN_REUSE_DETECTED
        - MFA_REQUIRED
        - MFA_INVALID
        - MFA_CHALLENGE_EXPIRED
        - MFA_CHALLENGE_ALREADY_USED
        - REAUTHENTICATION_REQUIRED
        - REAUTHENTICATION_INVALID
        - REAUTHENTICATION_EXPIRED
        - FORBIDDEN
        - RESOURCE_NOT_FOUND
        - RESOURCE_STATE_CONFLICT
        - IDEMPOTENCY_KEY_REQUIRED
        - IDEMPOTENCY_KEY_REUSED
        - CONCURRENT_MODIFICATION
        - INSUFFICIENT_BALANCE
        - ACCOUNT_NOT_ACTIVE
        - CUSTOMER_NOT_ACTIVE
        - ACCOUNT_OWNERSHIP_REQUIRED
        - ACCOUNT_HOLDER_LIMIT_REACHED
        - ACCOUNT_CLOSURE_NOT_ALLOWED
        - INVALID_ACCOUNT_NUMBER
        - INVALID_AMOUNT
        - INVALID_DATE_RANGE
        - PERIOD_LIMIT_EXCEEDED
        - LOAN_INCOME_COMMITMENT_EXCEEDED
        - LOAN_NOT_ELIGIBLE
        - INSTALLMENT_ALREADY_PAID
        - PARTIAL_PAYMENT_NOT_SUPPORTED
        - PAYMENT_NOT_CANCELLABLE
        - CARD_NOT_ACTIVE
        - CARD_LIMIT_EXCEEDED
        - REVERSAL_NOT_ELIGIBLE
        - REVERSAL_ALREADY_EXISTS
        - REVERSAL_LIMIT_NOT_CONFIGURED
        - REVERSAL_LIMIT_EXCEEDED
        - REVERSAL_INSUFFICIENT_FUNDS
        - ADMIN_REASON_REQUIRED
        - LAST_ADMIN_PROTECTION
        - RATE_LIMIT_EXCEEDED
        - INTERNAL_ERROR
        - SERVICE_UNAVAILABLE

    # ----- Identity and authentication -----

    UserRole:
      type: string
      enum: [CUSTOMER, ADMIN]

    UserStatus:
      type: string
      enum: [PENDING, ACTIVE, BLOCKED, SUSPENDED, CLOSED]

    CustomerStatus:
      type: string
      enum: [PENDING, ACTIVE, BLOCKED, SUSPENDED, CLOSED]

    RegisterRequest:
      type: object
      additionalProperties: false
      required: [name, email, documentNumber, password]
      properties:
        name:
          type: string
          minLength: 2
          maxLength: 160
        email:
          type: string
          format: email
          maxLength: 254
        documentNumber:
          type: string
          pattern: '^[0-9]{11}$'
        password:
          type: string
          minLength: 12
          maxLength: 128
          writeOnly: true

    LoginRequest:
      type: object
      additionalProperties: false
      required: [email, password]
      properties:
        email:
          type: string
          format: email
        password:
          type: string
          writeOnly: true
          minLength: 1
          maxLength: 128

    TotpVerificationRequest:
      type: object
      additionalProperties: false
      required: [code]
      properties:
        code:
          type: string
          pattern: '^[0-9]{6,8}$'

    LoginChallenge:
      type: object
      additionalProperties: false
      required: [challengeId, challengeType, status, expiresAt]
      properties:
        challengeId:
          $ref: '#/components/schemas/UUID'
        challengeType:
          type: string
          enum: [TOTP, RECOVERY_CODE]
        status:
          type: string
          const: PENDING
        expiresAt:
          $ref: '#/components/schemas/DateTime'

    LoginChallengeResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/LoginChallenge'

    TokenPair:
      type: object
      additionalProperties: false
      required: [accessToken, tokenType, expiresIn]
      properties:
        accessToken:
          type: string
        tokenType:
          type: string
          const: Bearer
        expiresIn:
          type: integer
          const: 600

    AuthenticationResponse:
      type: object
      required: [data]
      properties:
        data:
          allOf:
            - $ref: '#/components/schemas/TokenPair'

    AccessTokenResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/TokenPair'

    ReauthenticationRequest:
      type: object
      additionalProperties: false
      required: [password, action]
      properties:
        password:
          type: string
          writeOnly: true
          minLength: 1
        totpCode:
          type: string
          pattern: '^[0-9]{6,8}$'
        action:
          $ref: '#/components/schemas/ReauthenticationAction'

    ReauthenticationAction:
      type: string
      enum:
        - CUSTOMER_PROFILE_UPDATE
        - CUSTOMER_SECURITY_UPDATE
        - ACCOUNT_CLOSURE
        - ACCOUNT_HOLDER_EXIT
        - ADMIN_CUSTOMER_BLOCK
        - ADMIN_CUSTOMER_SUSPEND
        - ADMIN_CUSTOMER_CLOSE
        - ADMIN_ACCOUNT_BLOCK
        - ADMIN_ACCOUNT_SUSPEND
        - ADMIN_ACCOUNT_CLOSE
        - ADMIN_LOAN_APPROVAL
        - ADMIN_LOAN_REJECTION
        - ADMIN_TRANSACTION_REVERSAL
        - ADMIN_SETTINGS_UPDATE
        - ADMINISTRATOR_MANAGEMENT
        - OUTBOX_EVENT_REPROCESSING

    ReauthenticationResponse:
      type: object
      required: [data]
      properties:
        data:
          type: object
          additionalProperties: false
          required: [reauthenticationToken, expiresIn, action]
          properties:
            reauthenticationToken:
              type: string
            expiresIn:
              type: integer
              const: 300
            action:
              $ref: '#/components/schemas/ReauthenticationAction'

    AuthenticatedUserResponse:
      type: object
      required: [data]
      properties:
        data:
          type: object
          required: [id, email, role, userStatus]
          properties:
            id:
              $ref: '#/components/schemas/UUID'
            email:
              type: string
              format: email
            role:
              $ref: '#/components/schemas/UserRole'
            userStatus:
              $ref: '#/components/schemas/UserStatus'
            customerStatus:
              $ref: '#/components/schemas/CustomerStatus'
            twoFactorEnabled:
              type: boolean

    Session:
      type: object
      required: [id, createdAt, expiresAt, current]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        createdAt:
          $ref: '#/components/schemas/DateTime'
        expiresAt:
          $ref: '#/components/schemas/DateTime'
        lastUsedAt:
          $ref: '#/components/schemas/DateTime'
        current:
          type: boolean
        deviceDescription:
          type: string

    SessionListResponse:
      type: object
      required: [data]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/Session'

    TotpSetupResponse:
      type: object
      required: [data]
      properties:
        data:
          type: object
          required: [secret, provisioningUri]
          properties:
            secret:
              type: string
              writeOnly: true
            provisioningUri:
              type: string
              writeOnly: true

    RecoveryCodesResponse:
      type: object
      required: [data]
      properties:
        data:
          type: object
          required: [codes]
          properties:
            codes:
              type: array
              minItems: 1
              items:
                type: string
                writeOnly: true

    RecoveryCodeRequest:
      type: object
      additionalProperties: false
      required: [recoveryCode]
      properties:
        recoveryCode:
          type: string
          writeOnly: true

    PasswordRecoveryRequest:
      type: object
      additionalProperties: false
      required: [email]
      properties:
        email:
          type: string
          format: email

    PasswordResetRequest:
      type: object
      additionalProperties: false
      required: [token, newPassword]
      properties:
        token:
          type: string
        newPassword:
          type: string
          minLength: 12
          maxLength: 128
          writeOnly: true

    EmailVerificationRequest:
      type: object
      additionalProperties: false
      required: [token]
      properties:
        token:
          type: string

    # ----- Customer -----

    Customer:
      type: object
      required: [id, name, email, status]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        name:
          type: string
        email:
          type: string
          format: email
        status:
          $ref: '#/components/schemas/CustomerStatus'
        createdAt:
          $ref: '#/components/schemas/DateTime'

    CustomerResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/Customer'

    CustomerListResponse:
      type: object
      required: [data, meta]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/Customer'
        meta:
          $ref: '#/components/schemas/PageMeta'

    CustomerUpdateRequest:
      type: object
      additionalProperties: false
      minProperties: 1
      properties:
        name:
          type: string
          minLength: 2
          maxLength: 160
        phone:
          type: string
          maxLength: 30

    # ----- Accounts -----

    AccountType:
      type: string
      enum: [INDIVIDUAL, JOINT]

    AccountStatus:
      type: string
      enum: [PENDING, ACTIVE, BLOCKED, SUSPENDED, CLOSED]

    Account:
      type: object
      required: [id, accountNumber, type, status, balance, currency]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        accountNumber:
          type: string
          pattern: '^[0-9]{9}$'
        type:
          $ref: '#/components/schemas/AccountType'
        status:
          $ref: '#/components/schemas/AccountStatus'
        balance:
          $ref: '#/components/schemas/Money'
        currency:
          $ref: '#/components/schemas/Currency'
        createdAt:
          $ref: '#/components/schemas/DateTime'

    AccountResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/Account'

    AccountListResponse:
      type: object
      required: [data, meta]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/Account'
        meta:
          $ref: '#/components/schemas/PageMeta'

    AccountCreateRequest:
      type: object
      additionalProperties: false
      required: [type]
      properties:
        type:
          $ref: '#/components/schemas/AccountType'

    BalanceResponse:
      type: object
      required: [data]
      properties:
        data:
          type: object
          required: [accountId, balance, currency, updatedAt]
          properties:
            accountId:
              $ref: '#/components/schemas/UUID'
            balance:
              $ref: '#/components/schemas/Money'
            currency:
              $ref: '#/components/schemas/Currency'
            updatedAt:
              $ref: '#/components/schemas/DateTime'

    StatementItem:
      type: object
      required: [transactionId, type, status, amount, description, createdAt]
      properties:
        transactionId:
          $ref: '#/components/schemas/UUID'
        type:
          type: string
        status:
          $ref: '#/components/schemas/TransactionStatus'
        direction:
          type: string
          enum: [CREDIT, DEBIT]
        amount:
          $ref: '#/components/schemas/Money'
        description:
          type: string
        createdAt:
          $ref: '#/components/schemas/DateTime'

    StatementResponse:
      type: object
      required: [data, meta]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/StatementItem'
        meta:
          $ref: '#/components/schemas/PageMeta'

    AccountHolder:
      type: object
      required: [customerId, name, status]
      properties:
        customerId:
          $ref: '#/components/schemas/UUID'
        name:
          type: string
        status:
          type: string
          enum: [ACTIVE, INACTIVE]
        ownershipPercentage:
          type: number
          minimum: 0
          maximum: 100
          description: Percentual opcional; a soma dos percentuais definidos deve ser 100.

    AccountHolderResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/AccountHolder'

    AccountHolderListResponse:
      type: object
      required: [data]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/AccountHolder'

    AccountInvitationRequest:
      type: object
      additionalProperties: false
      required: [customerId]
      properties:
        customerId:
          $ref: '#/components/schemas/UUID'
        ownershipPercentage:
          type: number
          minimum: 0
          maximum: 100

    InvitationStatus:
      type: string
      enum: [PENDING, ACCEPTED, EXPIRED, CANCELLED]

    Invitation:
      type: object
      required: [id, status, expiresAt]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        status:
          $ref: '#/components/schemas/InvitationStatus'
        expiresAt:
          $ref: '#/components/schemas/DateTime'
        createdAt:
          $ref: '#/components/schemas/DateTime'

    InvitationResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/Invitation'

    InvitationListResponse:
      type: object
      required: [data]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/Invitation'

    ClosureConsentResponse:
      type: object
      required: [data]
      properties:
        data:
          type: object
          required: [accountId, consented, totalActiveHolders, totalConsents]
          properties:
            accountId:
              $ref: '#/components/schemas/UUID'
            consented:
              type: boolean
            totalActiveHolders:
              type: integer
            totalConsents:
              type: integer

    # ----- Transactions and Ledger-facing resources -----

    TransactionStatus:
      type: string
      enum: [PENDING, PROCESSING, COMPLETED, FAILED, REJECTED, CANCELLED]

    Transaction:
      type: object
      required: [id, type, status, amount, currency, createdAt]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        type:
          type: string
          enum:
            - TRANSFER
            - DEPOSIT
            - WITHDRAWAL
            - PAYMENT
            - CARD_PURCHASE
            - LOAN_DISBURSEMENT
            - INSTALLMENT_PAYMENT
            - REVERSAL
        status:
          $ref: '#/components/schemas/TransactionStatus'
        amount:
          $ref: '#/components/schemas/Money'
        currency:
          $ref: '#/components/schemas/Currency'
        accountId:
          oneOf:
            - $ref: '#/components/schemas/UUID'
            - type: 'null'
        initiatedByCustomerId:
          $ref: '#/components/schemas/UUID'
        description:
          type: string
        createdAt:
          $ref: '#/components/schemas/DateTime'
        completedAt:
          oneOf:
            - $ref: '#/components/schemas/DateTime'
            - type: 'null'

    TransactionResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/Transaction'

    TransactionListResponse:
      type: object
      required: [data, meta]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/Transaction'
        meta:
          $ref: '#/components/schemas/PageMeta'

    TransferRequest:
      type: object
      additionalProperties: false
      required: [sourceAccountId, destinationAccountId, amount]
      properties:
        sourceAccountId:
          $ref: '#/components/schemas/UUID'
        destinationAccountId:
          $ref: '#/components/schemas/UUID'
        amount:
          $ref: '#/components/schemas/Money'
        description:
          type: string
          maxLength: 200

    AccountAmountRequest:
      type: object
      additionalProperties: false
      required: [accountId, amount]
      properties:
        accountId:
          $ref: '#/components/schemas/UUID'
        amount:
          $ref: '#/components/schemas/Money'

    # ----- Payments -----

    PaymentStatus:
      type: string
      enum:
        - PENDING
        - SCHEDULED
        - PROCESSING
        - COMPLETED
        - FAILED
        - REJECTED
        - CANCELLED

    PaymentProvider:
      type: object
      required: [id, name, category, active]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        name:
          type: string
        category:
          type: string
          enum: [WATER, ENERGY, INTERNET, TELEPHONY, GAS, OTHER]
        active:
          type: boolean

    PaymentProviderListResponse:
      type: object
      required: [data]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/PaymentProvider'

    PaymentCreateRequest:
      type: object
      additionalProperties: false
      required: [accountId, providerId, amount, description]
      properties:
        accountId:
          $ref: '#/components/schemas/UUID'
        providerId:
          $ref: '#/components/schemas/UUID'
        amount:
          $ref: '#/components/schemas/Money'
        description:
          type: string
          maxLength: 200
        scheduledAt:
          oneOf:
            - $ref: '#/components/schemas/DateTime'
            - type: 'null'

    Payment:
      type: object
      required: [id, accountId, providerId, amount, status, createdAt]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        accountId:
          $ref: '#/components/schemas/UUID'
        providerId:
          $ref: '#/components/schemas/UUID'
        amount:
          $ref: '#/components/schemas/Money'
        status:
          $ref: '#/components/schemas/PaymentStatus'
        description:
          type: string
        scheduledAt:
          oneOf:
            - $ref: '#/components/schemas/DateTime'
            - type: 'null'
        createdAt:
          $ref: '#/components/schemas/DateTime'
        completedAt:
          oneOf:
            - $ref: '#/components/schemas/DateTime'
            - type: 'null'
        statusUrl:
          type: string
          format: uri-reference

    PaymentResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/Payment'

    PaymentListResponse:
      type: object
      required: [data, meta]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/Payment'
        meta:
          $ref: '#/components/schemas/PageMeta'

    # ----- Loans -----

    LoanStatus:
      type: string
      enum:
        - REQUESTED
        - UNDER_REVIEW
        - APPROVED
        - REJECTED
        - CANCELLED
        - CONTRACTED
        - ACTIVE
        - PAID_OFF

    InstallmentStatus:
      type: string
      enum: [PENDING, PAID, OVERDUE, CANCELLED]

    LoanSimulationRequest:
      type: object
      additionalProperties: false
      required: [principalAmount, installmentsCount]
      properties:
        principalAmount:
          $ref: '#/components/schemas/Money'
        installmentsCount:
          type: integer
          minimum: 1

    LoanSimulation:
      type: object
      required:
        - principalAmount
        - installmentsCount
        - monthlyInterestRate
        - installmentAmount
        - totalAmount
        - totalInterest
        - amortizationSystem
      properties:
        principalAmount:
          $ref: '#/components/schemas/Money'
        installmentsCount:
          type: integer
        monthlyInterestRate:
          type: string
          description: Taxa percentual mensal em representação decimal textual.
        installmentAmount:
          $ref: '#/components/schemas/Money'
        totalAmount:
          $ref: '#/components/schemas/Money'
        totalInterest:
          $ref: '#/components/schemas/Money'
        amortizationSystem:
          type: string
          const: PRICE

    LoanSimulationResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/LoanSimulation'

    LoanRequest:
      type: object
      additionalProperties: false
      required: [principalAmount, installmentsCount, declaredMonthlyIncome]
      properties:
        principalAmount:
          $ref: '#/components/schemas/Money'
        installmentsCount:
          type: integer
          minimum: 1
        declaredMonthlyIncome:
          $ref: '#/components/schemas/Money'

    Loan:
      type: object
      required: [id, principalAmount, installmentsCount, status]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        principalAmount:
          $ref: '#/components/schemas/Money'
        installmentsCount:
          type: integer
        status:
          $ref: '#/components/schemas/LoanStatus'
        monthlyInstallment:
          $ref: '#/components/schemas/Money'
        createdAt:
          $ref: '#/components/schemas/DateTime'

    LoanResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/Loan'

    LoanListResponse:
      type: object
      required: [data, meta]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/Loan'
        meta:
          $ref: '#/components/schemas/PageMeta'

    LoanInstallment:
      type: object
      required: [id, number, dueDate, amount, status]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        number:
          type: integer
          minimum: 1
        dueDate:
          type: string
          format: date
        amount:
          $ref: '#/components/schemas/Money'
        status:
          $ref: '#/components/schemas/InstallmentStatus'
        paidAt:
          oneOf:
            - $ref: '#/components/schemas/DateTime'
            - type: 'null'

    InstallmentResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/LoanInstallment'

    InstallmentListResponse:
      type: object
      required: [data]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/LoanInstallment'

    InstallmentPaymentRequest:
      type: object
      additionalProperties: false
      required: [paymentMethod]
      properties:
        paymentMethod:
          type: string
          const: SIMULATED_SETTLEMENT

    InstallmentPayment:
      type: object
      required: [paymentId, loanId, installmentId, amount, status, paymentMethod]
      properties:
        paymentId:
          $ref: '#/components/schemas/UUID'
        loanId:
          $ref: '#/components/schemas/UUID'
        installmentId:
          $ref: '#/components/schemas/UUID'
        amount:
          $ref: '#/components/schemas/Money'
        status:
          type: string
          enum: [COMPLETED, FAILED, REJECTED]
        paymentMethod:
          type: string
          const: SIMULATED_SETTLEMENT
        paidAt:
          oneOf:
            - $ref: '#/components/schemas/DateTime'
            - type: 'null'

    InstallmentPaymentResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/InstallmentPayment'

    InstallmentPaymentListResponse:
      type: object
      required: [data]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/InstallmentPayment'

    LoanContractResponse:
      type: object
      required: [data]
      properties:
        data:
          type: object
          required: [loanId, contractVersion, principalAmount, installmentAmount, installmentsCount, amortizationSystem]
          properties:
            loanId:
              $ref: '#/components/schemas/UUID'
            contractVersion:
              type: integer
            principalAmount:
              $ref: '#/components/schemas/Money'
            installmentAmount:
              $ref: '#/components/schemas/Money'
            installmentsCount:
              type: integer
            monthlyInterestRate:
              type: string
            totalAmount:
              $ref: '#/components/schemas/Money'
            amortizationSystem:
              type: string
              const: PRICE
            contractedAt:
              $ref: '#/components/schemas/DateTime'

    # ----- Cards -----

    CardStatus:
      type: string
      enum: [ACTIVE, BLOCKED, CANCELLED]

    CardCreateRequest:
      type: object
      additionalProperties: false
      required: [accountId, spendingLimit]
      properties:
        accountId:
          $ref: '#/components/schemas/UUID'
        spendingLimit:
          $ref: '#/components/schemas/Money'

    Card:
      type: object
      required: [id, displayNumber, status, spendingLimit, availableLimit]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        displayNumber:
          type: string
          description: Identificador demonstrativo, sem PAN real.
        status:
          $ref: '#/components/schemas/CardStatus'
        spendingLimit:
          $ref: '#/components/schemas/Money'
        availableLimit:
          $ref: '#/components/schemas/Money'
        createdAt:
          $ref: '#/components/schemas/DateTime'

    CardResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/Card'

    CardListResponse:
      type: object
      required: [data]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/Card'

    CardPurchaseRequest:
      type: object
      additionalProperties: false
      required: [amount, description]
      properties:
        amount:
          $ref: '#/components/schemas/Money'
        description:
          type: string
          maxLength: 200

    CardTransaction:
      type: object
      required: [id, cardId, amount, status, createdAt]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        cardId:
          $ref: '#/components/schemas/UUID'
        amount:
          $ref: '#/components/schemas/Money'
        status:
          $ref: '#/components/schemas/TransactionStatus'
        description:
          type: string
        createdAt:
          $ref: '#/components/schemas/DateTime'

    CardTransactionResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/CardTransaction'

    CardTransactionListResponse:
      type: object
      required: [data, meta]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/CardTransaction'
        meta:
          $ref: '#/components/schemas/PageMeta'

    CardReversalRequest:
      type: object
      additionalProperties: false
      required: [reason, confirmation]
      properties:
        reason:
          type: string
          minLength: 10
          maxLength: 500
        confirmation:
          type: boolean
          const: true

    # ----- Reversals -----

    ReversalRequest:
      type: object
      additionalProperties: false
      required: [reason, confirmation]
      properties:
        reason:
          type: string
          minLength: 10
          maxLength: 500
        confirmation:
          type: boolean
          const: true

    Reversal:
      type: object
      required: [id, originalTransactionId, reversalTransactionId, amount, status]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        originalTransactionId:
          $ref: '#/components/schemas/UUID'
        reversalTransactionId:
          $ref: '#/components/schemas/UUID'
        amount:
          $ref: '#/components/schemas/Money'
        status:
          type: string
          enum: [COMPLETED, FAILED, REJECTED]
        reason:
          type: string
        createdAt:
          $ref: '#/components/schemas/DateTime'

    ReversalResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/Reversal'

    # ----- Administration -----

    OutboxReprocessRequest:
      type: object
      additionalProperties: false
      required: [reason, confirmation]
      properties:
        reason:
          type: string
          minLength: 10
          maxLength: 500
        confirmation:
          type: boolean
          const: true

    OutboxReprocessAcceptedResponse:
      type: object
      additionalProperties: false
      required: [eventId, status, message, statusUrl]
      properties:
        eventId:
          $ref: '#/components/schemas/UUID'
        status:
          type: string
          const: PENDING
        message:
          type: string
        statusUrl:
          type: string
          format: uri-reference
          description: Caminho versionado para consultar estado e tentativas do evento.

    OutboxEvent:
      type: object
      required: [eventId, status]
      properties:
        eventId:
          $ref: '#/components/schemas/UUID'
        status:
          type: string
          description: Estado da Outbox; enumeração depende da harmonização pendente entre FAILED e DEAD_LETTER.
        attempts:
          type: array
          description: Histórico preservado das tentativas de processamento.
          items:
            type: object
            additionalProperties: true
        lastError:
          type: string
          description: Erro operacional apresentado conforme política de acesso e redação de dados sensíveis.

    OutboxEventResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/OutboxEvent'

    AdminReasonRequest:
      type: object
      additionalProperties: false
      required: [reason]
      properties:
        reason:
          type: string
          minLength: 10
          maxLength: 500

    AdminInvitationRequest:
      type: object
      additionalProperties: false
      required: [email]
      properties:
        email:
          type: string
          format: email

    Administrator:
      type: object
      required: [userId, email, status, twoFactorEnabled]
      properties:
        userId:
          $ref: '#/components/schemas/UUID'
        email:
          type: string
          format: email
        status:
          $ref: '#/components/schemas/UserStatus'
        twoFactorEnabled:
          type: boolean
          const: true

    AdministratorListResponse:
      type: object
      required: [data]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/Administrator'

    Setting:
      type: object
      required: [key, configured, updatedAt]
      properties:
        key:
          type: string
        configured:
          type: boolean
        value:
          description: Valor conforme o tipo da configuração.
        updatedAt:
          $ref: '#/components/schemas/DateTime'

    SettingResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/Setting'

    SettingsListResponse:
      type: object
      required: [data]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/Setting'

    SettingUpdateRequest:
      type: object
      additionalProperties: false
      required: [value, reason]
      properties:
        value:
          description: Valor validado de acordo com o schema da configuração.
        reason:
          type: string
          minLength: 10
          maxLength: 500

    # ----- Notifications -----

    Notification:
      type: object
      required: [id, title, message, read, createdAt]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        title:
          type: string
        message:
          type: string
        read:
          type: boolean
        createdAt:
          $ref: '#/components/schemas/DateTime'

    NotificationResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/Notification'

    NotificationListResponse:
      type: object
      required: [data, meta]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/Notification'
        meta:
          $ref: '#/components/schemas/PageMeta'

    NotificationPreferencesResponse:
      type: object
      required: [data]
      properties:
        data:
          type: object
          properties:
            inAppEnabled:
              type: boolean

    NotificationPreferencesUpdateRequest:
      type: object
      additionalProperties: false
      minProperties: 1
      properties:
        inAppEnabled:
          type: boolean

    # ----- Audit -----

    AuditEvent:
      type: object
      required: [id, actorId, eventType, result, occurredAt]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        actorId:
          $ref: '#/components/schemas/UUID'
        eventType:
          type: string
        resourceType:
          type: string
        resourceId:
          type: string
        result:
          type: string
          enum: [SUCCESS, FAILURE]
        correlationId:
          $ref: '#/components/schemas/UUID'
        occurredAt:
          $ref: '#/components/schemas/DateTime'

    AuditEventResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/AuditEvent'

    AuditEventListResponse:
      type: object
      required: [data, meta]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/AuditEvent'
        meta:
          $ref: '#/components/schemas/PageMeta'

    # ----- Privacy -----

    PrivacyRequestCreate:
      type: object
      additionalProperties: false
      required: [requestType, description]
      properties:
        requestType:
          type: string
          enum:
            - ACCESS
            - CORRECTION
            - ANONYMIZATION
            - DELETION
            - PORTABILITY
            - INFORMATION
        description:
          type: string
          minLength: 10
          maxLength: 1000

    PrivacyRequest:
      type: object
      required: [id, requestType, status, createdAt]
      properties:
        id:
          $ref: '#/components/schemas/UUID'
        requestType:
          type: string
        status:
          type: string
          enum: [RECEIVED, UNDER_REVIEW, APPROVED, REJECTED, COMPLETED]
        createdAt:
          $ref: '#/components/schemas/DateTime'

    PrivacyRequestResponse:
      type: object
      required: [data]
      properties:
        data:
          $ref: '#/components/schemas/PrivacyRequest'

    PrivacyRequestListResponse:
      type: object
      required: [data]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/PrivacyRequest'
```

---

# 4. Catálogo de erros de negócio

A tabela abaixo complementa o enum `ErrorCode` da especificação.

| Código | HTTP | Situação |
|---|---:|---|
| `VALIDATION_ERROR` | 400 | Dados inválidos |
| `INVALID_CREDENTIALS` | 401 | Credenciais inválidas |
| `TOKEN_EXPIRED` | 401 | Token expirado |
| `TOKEN_REVOKED` | 401 | Token revogado |
| `TOKEN_REUSE_DETECTED` | 401 | Reutilização de Refresh Token |
| `MFA_REQUIRED` | 401 | Segundo fator necessário |
| `MFA_INVALID` | 401 | Código TOTP inválido |
| `MFA_CHALLENGE_EXPIRED` | 401 | Desafio expirado |
| `REAUTHENTICATION_REQUIRED` | 401 | Reautenticação necessária |
| `FORBIDDEN` | 403 | Acesso não autorizado |
| `RESOURCE_NOT_FOUND` | 404 | Recurso inexistente ou não acessível |
| `RESOURCE_STATE_CONFLICT` | 409 | Transição de estado inválida |
| `IDEMPOTENCY_KEY_REUSED` | 409 | Chave utilizada com conteúdo diferente |
| `CONCURRENT_MODIFICATION` | 409 | Conflito de concorrência |
| `INSUFFICIENT_BALANCE` | 422 | Saldo insuficiente |
| `ACCOUNT_NOT_ACTIVE` | 422 | Conta sem estado operacional |
| `CUSTOMER_NOT_ACTIVE` | 422 | Cliente sem estado operacional |
| `ACCOUNT_OWNERSHIP_REQUIRED` | 403 | Usuário não é titular autorizado |
| `LOAN_INCOME_COMMITMENT_EXCEEDED` | 422 | Comprometimento de renda excedido |
| `INSTALLMENT_ALREADY_PAID` | 409 | Parcela já quitada |
| `PARTIAL_PAYMENT_NOT_SUPPORTED` | 422 | Pagamento parcial não suportado |
| `CARD_LIMIT_EXCEEDED` | 422 | Limite de gasto excedido |
| `REVERSAL_NOT_ELIGIBLE` | 422 | Transação não elegível para estorno |
| `REVERSAL_ALREADY_EXISTS` | 409 | Estorno integral já realizado |
| `REVERSAL_LIMIT_NOT_CONFIGURED` | 503 | Política obrigatória de estorno incompleta |
| `REVERSAL_LIMIT_EXCEEDED` | 422 | Limite de estorno excedido |
| `REVERSAL_INSUFFICIENT_FUNDS` | 422 | Compensação provocaria saldo negativo |
| `ADMIN_REASON_REQUIRED` | 422 | Justificativa obrigatória ausente |
| `LAST_ADMIN_PROTECTION` | 409 | Operação deixaria o sistema sem ADMIN ativo |
| `RATE_LIMIT_EXCEEDED` | 429 | Limite de requisições excedido |
| `INTERNAL_ERROR` | 500 | Erro interno |
| `SERVICE_UNAVAILABLE` | 503 | Serviço indisponível |

Observação: o código de erro deverá permanecer estável. O texto de `title` e `detail` poderá ser ajustado sem quebrar os consumidores.

# 5. Matriz de segurança dos endpoints

A matriz define o comportamento esperado. A autorização efetiva deverá ser implementada e testada no backend.

| Grupo de endpoints | Perfil | Autenticação | Titularidade/escopo | 2FA | Reautenticação |
|---|---|---|---|---|---|
| Cadastro e login | Público | Não | Não | Conforme usuário | Não |
| Desafio de login | Público com challenge | Challenge válido | Identidade do challenge | Sim | Não |
| Refresh e logout | Sessão | Cookie de refresh | Própria sessão | Não | Não |
| Perfil do cliente | CUSTOMER | Sim | Próprio cliente | Não | Para alterações sensíveis |
| Contas | CUSTOMER | Sim | Titular ativo | Conforme risco | Encerramento e saída |
| Convites de conta | CUSTOMER | Sim | Titular ativo ou destinatário | Conforme risco | Conforme risco |
| Transferências | CUSTOMER | Sim | Titular ativo da origem | Conforme política de risco | Conforme política de risco |
| Depósitos e saques | CUSTOMER | Sim | Titular ativo | Conforme política de risco | Conforme política de risco |
| Pagamentos | CUSTOMER | Sim | Titular ativo | Conforme política de risco | Conforme política de risco |
| Empréstimos do cliente | CUSTOMER | Sim | Próprio cliente | Conforme risco | Conforme risco |
| Pagamento de parcelas | CUSTOMER | Sim | Próprio empréstimo | Conforme política de risco | Conforme política de risco |
| Cartões | CUSTOMER | Sim | Próprio cartão e conta | Conforme política de risco | Conforme risco |
| Estorno administrativo | ADMIN | Sim | Escopo administrativo | Obrigatório | Obrigatória |
| Administração de clientes | ADMIN | Sim | Escopo administrativo | Obrigatório | Para ações críticas |
| Administração de contas | ADMIN | Sim | Escopo administrativo | Obrigatório | Para ações críticas |
| Administração de empréstimos | ADMIN | Sim | Escopo administrativo | Obrigatório | Aprovação/rejeição |
| Administração de usuários ADMIN | ADMIN | Sim | Escopo administrativo | Obrigatório | Obrigatória |
| Configurações críticas | ADMIN | Sim | Escopo administrativo | Obrigatório | Obrigatória |
| Auditoria | ADMIN | Sim | Escopo administrativo | Obrigatório | Conforme criticidade |
| Privacidade do cliente | CUSTOMER | Sim | Próprio titular | Conforme risco | Conforme solicitação |

**Importante:** ADMIN não poderá alterar saldo diretamente. Qualquer efeito financeiro deverá passar pelos casos de uso e pelo Ledger.

# 6. Regras transversais de contrato

## 6.1. Valores monetários

- Representação textual decimal com duas casas.
- Sem notação científica.
- Sem valores negativos em operações de crédito ou débito comuns.
- Precisão compatível com `NUMERIC(19,2)`.
- Backend converte e valida para `BigDecimal`.
- Frontend não decide o saldo final.

## 6.2. Idempotência

Para operações financeiras:

- Cabeçalho obrigatório `Idempotency-Key`.
- Chave vinculada ao usuário e ao tipo de operação.
- Requisição repetida com mesmo conteúdo retorna o resultado original.
- Reutilização com conteúdo diferente resulta em HTTP 409.
- Persistência do resultado idempotente na mesma fronteira transacional da operação.

## 6.3. Processamento

- Operações financeiras principais são síncronas.
- HTTP 202 somente representa operação ainda pendente.
- Agendamento criado com sucesso pode retornar HTTP 201.
- Notificações e Outbox são assíncronas.
- O backend nunca informa `COMPLETED` antes da contabilização.

## 6.4. Extrato

- Padrão: últimos 30 dias.
- Máximo: 365 dias.
- Datas inclusivas.
- Fuso de interpretação: `America/Sao_Paulo`.
- Página inicial zero.
- Tamanho padrão 20, máximo 100.
- Ordenação padrão `createdAt,desc`.
- Datas, valores e campos de ordenação validados.

## 6.5. Pagamento de parcelas

- Somente integral.
- Valor calculado pelo backend.
- `PENDING` ou `OVERDUE` são estados elegíveis.
- A parcela somente passa a `PAID` após contabilização.
- Pagamento idempotente.
- Liquidação simulada sem dependência de conta ativa.
- Nenhum campo `amount` é aceito na requisição.

## 6.6. Estornos

- Integrais na primeira versão.
- Uma reversão por transação original.
- Apenas transações `COMPLETED`.
- Sem estorno de estorno.
- Sem saldo negativo.
- Sem alteração de Ledger original.
- Reautenticação, justificativa, confirmação, idempotência e auditoria.
- Limites configuráveis e obrigatórios.
- Empréstimos tratados em fluxos específicos.

## 6.7. Estados

A API não aceitará atualização direta de campos `status` por endpoints genéricos de edição.

Transições ocorrerão por operações específicas e serão validadas no domínio.

---

# 7. Critérios de validação da especificação

Antes de aprovar a OpenAPI, deverão ser verificados:

1. Validade sintática YAML.
2. Compatibilidade com OpenAPI 3.1.1.
3. Resolução de todas as referências `$ref`.
4. Ausência de `operationId` duplicado.
5. Ausência de parâmetros de caminho sem declaração.
6. Schemas de requisição e resposta coerentes.
7. Códigos HTTP compatíveis com os fluxos.
8. Segurança definida por operação.
9. Cookies e cabeçalhos documentados corretamente.
10. Exemplos compatíveis com os respectivos schemas.
11. Consistência com os contratos REST v1.1.
12. Consistência com a especificação funcional.
13. Consistência com o DER e dicionário de dados.
14. Ausência de endpoints para operações fora de escopo.
15. Ausência de operações administrativas que alterem saldo diretamente.

# 8. Observações para revisão

Esta proposta formaliza a estrutura e os principais contratos, mas há decisões de detalhamento que precisam de validação antes de se tornar a referência definitiva:

- A política de segurança por endpoint deverá ser fechada individualmente, inclusive para operações de consulta sensível.
- Os limites de estorno permanecerão sem números até serem definidos em configuração administrativa.
- A semântica de `201`, `200` e `202` deverá ser confirmada em cada operação, especialmente em pagamentos e contratação de empréstimos.
- A representação do segredo TOTP e dos códigos de recuperação deverá ser protegida para que dados de provisionamento sejam retornados somente no momento apropriado.
- A lista de códigos de erro deverá ser comparada com todos os casos de uso para eliminar códigos ausentes ou redundantes.
- A especificação completa deverá ser validada por ferramenta compatível com OpenAPI 3.1.1 antes da aprovação final.

**Fim da proposta — MyBank OpenAPI 3.1.1**