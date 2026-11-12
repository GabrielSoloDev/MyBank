# MyBank — Especificação Consolidada dos Contratos da API REST

| Campo | Valor |
| --- | --- |
| Identificador | MB-API-REST |
| Classe | Documento oficial da biblioteca MyBank |
| Original preservado | [MyBank — Especificação Consolidada dos Contratos da API REST.md](../99-fontes-originais/MyBank%20—%20Especificação%20Consolidada%20dos%20Contratos%20da%20API%20REST.md) |
| Versão | 1.1 |
| Status | Proposta consolidada para revisão |
| Padrão | REST |
| Formato | JSON |
| Especificação futura | OpenAPI 3.1.1 |
| Prefixo | `/api/v1` |
| Moeda | BRL |
| Arquitetura | Hexagonal + Monólito Modular |

---

## Sumário

- [1. Objetivo e abrangência](#1-objetivo-e-abrangencia)
- [2. Convenções gerais](#2-convencoes-gerais)
- [2.1. Endereçamento](#21-enderecamento)
- [2.2. Formato de dados](#22-formato-de-dados)
- [2.3. Métodos HTTP](#23-metodos-http)
- [2.4. Cabeçalhos](#24-cabecalhos)
- [3. Padronização de respostas](#3-padronizacao-de-respostas)
- [3.1. Sucesso](#31-sucesso)
- [3.2. Erros](#32-erros)
- [3.3. Códigos HTTP](#33-codigos-http)
- [4. Autenticação e identidade](#4-autenticacao-e-identidade)
- [4.1. Cadastro e autenticação](#41-cadastro-e-autenticacao)
- [4.2. Login em duas etapas](#42-login-em-duas-etapas)
- [4.3. Validação do desafio](#43-validacao-do-desafio)
- [4.4. Política de autenticação por perfil](#44-politica-de-autenticacao-por-perfil)
- [4.5. Tokens e sessões](#45-tokens-e-sessoes)
- [4.6. Segundo fator](#46-segundo-fator)
- [4.7. Recuperação de senha e e-mail](#47-recuperacao-de-senha-e-e-mail)
- [5. Reautenticação contextual](#5-reautenticacao-contextual)
- [5.1. Endpoint](#51-endpoint)
- [5.2. Regras](#52-regras)
- [5.3. Matriz de reautenticação](#53-matriz-de-reautenticacao)
- [6. Clientes](#6-clientes)
- [7. Contas bancárias](#7-contas-bancarias)
- [7.1. Endpoints](#71-endpoints)
- [7.2. Saldo](#72-saldo)
- [7.3. Extrato](#73-extrato)
- [7.4. Contas conjuntas](#74-contas-conjuntas)
- [8. Transações financeiras](#8-transacoes-financeiras)
- [8.1. Transferências](#81-transferencias)
- [8.2. Depósitos simulados](#82-depositos-simulados)
- [8.3. Saques simulados](#83-saques-simulados)
- [8.4. Processamento financeiro](#84-processamento-financeiro)
- [9. Pagamentos](#9-pagamentos)
- [9.1. Endpoints](#91-endpoints)
- [10. Empréstimos](#10-emprestimos)
- [10.1. Endpoints do cliente](#101-endpoints-do-cliente)
- [10.2. Simulação](#102-simulacao)
- [10.3. Solicitação](#103-solicitacao)
- [10.4. Administração](#104-administracao)
- [10.5. Pagamento integral de parcelas](#105-pagamento-integral-de-parcelas)
- [11. Cartões virtuais](#11-cartoes-virtuais)
- [11.1. Endpoints](#111-endpoints)
- [12. Estornos administrativos](#12-estornos-administrativos)
- [12.1. Endpoint](#121-endpoint)
- [12.2. Política](#122-politica)
- [12.3. Categorias elegíveis propostas e aprovadas](#123-categorias-elegiveis-propostas-e-aprovadas)
- [12.4. Limites configuráveis](#124-limites-configuraveis)
- [12.5. Fluxo de execução](#125-fluxo-de-execucao)
- [13. Notificações](#13-notificacoes)
- [14. Administração](#14-administracao)
- [14.1. Clientes](#141-clientes)
- [14.2. Contas](#142-contas)
- [14.3. Administradores](#143-administradores)
- [14.4. Configurações](#144-configuracoes)
- [15. Auditoria e privacidade](#15-auditoria-e-privacidade)
- [15.1. Auditoria](#151-auditoria)
- [15.2. Privacidade](#152-privacidade)
- [16. Processamento síncrono e assíncrono](#16-processamento-sincrono-e-assincrono)
- [16.1. Operações síncronas](#161-operacoes-sincronas)
- [16.2. Operações assíncronas](#162-operacoes-assincronas)
- [16.3. HTTP 202](#163-http-202)
- [17. Idempotência e concorrência](#17-idempotencia-e-concorrencia)
- [17.1. Idempotência](#171-idempotencia)
- [17.2. Concorrência](#172-concorrencia)
- [18. Paginação, filtros e ordenação](#18-paginacao-filtros-e-ordenacao)
- [19. Máquinas de estados](#19-maquinas-de-estados)
- [19.1. Transações](#191-transacoes)
- [19.2. Pagamentos](#192-pagamentos)
- [19.3. Empréstimos](#193-emprestimos)
- [19.4. Parcelas](#194-parcelas)
- [19.5. Convites](#195-convites)
- [19.6. Cartões](#196-cartoes)
- [19.7. Clientes](#197-clientes)
- [19.8. Contas](#198-contas)
- [20. Segurança dos contratos](#20-seguranca-dos-contratos)
- [21. Outbox e eventos](#21-outbox-e-eventos)
- [22. Arquitetura de implementação](#22-arquitetura-de-implementacao)
- [22.1. Adaptadores de entrada REST](#221-adaptadores-de-entrada-rest)
- [22.2. Camada de aplicação](#222-camada-de-aplicacao)
- [22.3. Domínio](#223-dominio)
- [22.4. Adaptadores de saída](#224-adaptadores-de-saida)
- [23. Organização modular dos endpoints](#23-organizacao-modular-dos-endpoints)
- [24. Estratégia de testes dos contratos](#24-estrategia-de-testes-dos-contratos)
- [25. Pendências e limites da especificação](#25-pendencias-e-limites-da-especificacao)
- [26. Critérios de aceite](#26-criterios-de-aceite)
- [27. Decisões complementares aprovadas — reprocessamento da Outbox](#27-decisoes-complementares-aprovadas-reprocessamento-da-outbox)
- [Endpoint](#endpoint)

## 1. Objetivo e abrangência

Este documento define os contratos de comunicação entre o frontend React e o backend Spring Boot do MyBank.

Estabelece:

- Organização dos recursos e endpoints.
- Métodos HTTP.
- Estruturas de requisição e resposta.
- Autenticação e autorização.
- Regras de validação.
- Códigos HTTP e erros.
- Idempotência e concorrência.
- Processamento síncrono e assíncrono.
- Paginação, filtros e ordenação.
- Segurança e auditoria.
- Integração dos contratos com os módulos internos.

A API representa um banco digital fictício, destinado a fins educacionais e de portfólio.

Não existem operações bancárias reais, integração com instituições financeiras externas, Pix real, boletos reais, cartões físicos ou conversão de moedas.

## 2. Convenções gerais

## 2.1. Endereçamento

Todos os endpoints públicos deverão utilizar:

`/api/v1`

Organização dos recursos:

| Prefixo | Módulo |
| --- | --- |
| `/auth` | Identity |
| `/customers` | Customer |
| `/accounts` | Account |
| `/account-invitations` | Account |
| `/transactions` | Transaction |
| `/transfers` | Transaction |
| `/deposits` | Transaction |
| `/withdrawals` | Transaction |
| `/payments` | Payment |
| `/payment-providers` | Payment |
| `/loans` | Loan |
| `/cards` | Card |
| `/card-transactions` | Card |
| `/notifications` | Notification |
| `/admin` | Admin |
| `/privacy` | Privacy |

Os recursos internos do Ledger não terão endpoints públicos para alteração direta de lançamentos.

## 2.2. Formato de dados

- JSON como formato de comunicação.
- UTF-8.
- Identificadores internos UUID.
- Campos em `camelCase`.
- Datas e horários em ISO 8601.
- Moeda fixa BRL.
- Valores monetários representados como decimais, sem ponto flutuante.
- Valores monetários enviados pelo backend como strings decimais para evitar perda de precisão no frontend.

Exemplos:

```json
{
  "amount": "1250.75",
  "currency": "BRL",
  "createdAt": "2026-11-12T14:30:00Z"
}

```

O frontend não deverá utilizar `float` ou `double` para cálculos financeiros. A representação monetária deverá preservar a precisão decimal.

## 2.3. Métodos HTTP

| Método | Finalidade |
| --- | --- |
| GET | Consultar recursos |
| POST | Criar recursos ou executar operações |
| PUT | Substituir integralmente um recurso, quando aplicável |
| PATCH | Atualizar parcialmente um recurso |
| DELETE | Excluir recursos somente quando permitido pelas regras |

Operações financeiras contabilizadas não poderão ser alteradas ou removidas por `PUT`, `PATCH` ou `DELETE`.

Correções financeiras serão realizadas por operações compensatórias.

## 2.4. Cabeçalhos

Cabeçalhos gerais:

```http
Content-Type: application/json
Accept: application/json
Authorization: Bearer <access_token>
X-Correlation-ID: <uuid>

```

Cabeçalhos adicionais:

| Cabeçalho | Utilização |
| --- | --- |
| `Idempotency-Key` | Operações financeiras e operações críticas que exigem execução única |
| `X-Reauthentication-Token` | Operações que exigem reautenticação |
| `X-Correlation-ID` | Rastreamento de requisições e eventos |

O backend deverá gerar um identificador de correlação quando o cliente não fornecer um.

## 3. Padronização de respostas

## 3.1. Sucesso

Formato geral:

```json
{
  "data": {
    "id": "uuid",
    "status": "ACTIVE"
  },
  "meta": {
    "correlationId": "uuid",
    "timestamp": "2026-11-12T14:30:00Z"
  }
}

```

Respostas de listagem poderão incluir informações de paginação em `meta`.

Operações concluídas sem necessidade de retorno utilizarão HTTP 204.

## 3.2. Erros

A API adotará o padrão conceitual Problem Details, com campos adicionais de identificação e rastreamento.

```json
{
  "type": "https://mybank.example/errors/business-rule",
  "title": "Regra de negócio não atendida",
  "status": 422,
  "detail": "Saldo insuficiente para realizar a operação.",
  "instance": "/api/v1/transfers",
  "code": "INSUFFICIENT_BALANCE",
  "correlationId": "uuid",
  "timestamp": "2026-11-12T14:30:00Z",
  "errors": []
}

```

Regras:

- Não retornar stack traces.
- Não expor consultas SQL.
- Não revelar detalhes internos da aplicação.
- Não expor informações de outros clientes.
- Utilizar códigos de erro estáveis.
- Não revelar se um usuário ou e-mail está cadastrado em operações de autenticação e recuperação.

## 3.3. Códigos HTTP

| Código | Significado |
| --- | --- |
| 200 | Operação concluída |
| 201 | Recurso criado |
| 202 | Operação aceita para processamento posterior |
| 204 | Operação concluída sem conteúdo |
| 400 | Requisição inválida |
| 401 | Não autenticado ou autenticação adicional necessária |
| 403 | Acesso não autorizado |
| 404 | Recurso inexistente ou não acessível |
| 409 | Conflito de estado, idempotência ou concorrência |
| 422 | Regra de negócio não atendida |
| 429 | Limite de requisições excedido |
| 500 | Erro interno |
| 503 | Serviço temporariamente indisponível |

A API não deverá retornar HTTP 200 para representar uma operação financeira rejeitada ou com falha.

## 4. Autenticação e identidade

## 4.1. Cadastro e autenticação

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| POST | `/auth/register` | Registrar cliente |
| POST | `/auth/login` | Autenticar usuário |
| POST | `/auth/login/challenges/{challengeId}/verify` | Validar desafio de autenticação |
| POST | `/auth/refresh` | Renovar sessão |
| POST | `/auth/logout` | Encerrar sessão atual |
| POST | `/auth/logout-all` | Revogar todas as sessões |
| POST | `/auth/reauthentication` | Solicitar reautenticação |
| GET | `/auth/me` | Consultar identidade autenticada |
| GET | `/auth/sessions` | Listar sessões |
| DELETE | `/auth/sessions/{sessionId}` | Revogar sessão |

## 4.2. Login em duas etapas

`POST /api/v1/auth/login`

Requisição:

```json
{
  "email": "cliente@example.com",
  "password": "senha"
}

```

O backend deverá verificar:

- Existência e estado da identidade.
- Credenciais.
- Estado do cliente, quando aplicável.
- Necessidade de segundo fator.
- Políticas de autenticação.
- Restrições da sessão.

Se não houver exigência de segundo fator, a sessão poderá ser estabelecida normalmente.

Se houver exigência de 2FA, o backend responderá HTTP 202:

```json
{
  "data": {
    "challengeId": "uuid",
    "challengeType": "TOTP",
    "status": "PENDING",
    "expiresAt": "2026-11-12T14:35:00Z"
  }
}

```

O desafio temporário não representa uma sessão autenticada definitiva.

## 4.3. Validação do desafio

`POST /api/v1/auth/login/challenges/{challengeId}/verify`

Requisição:

```json
{
  "code": "123456"
}

```

Resposta de sucesso:

```json
{
  "data": {
    "authenticated": true,
    "accessToken": "jwt",
    "tokenType": "Bearer",
    "expiresIn": 600
  }
}

```

O Refresh Token será enviado por cookie seguro.

Regras:

- Validade inicial do desafio: 5 minutos.
- Desafio vinculado à identidade e à tentativa de autenticação.
- Uso único.
- Limitação de tentativas.
- Desafios expirados ou concluídos não poderão ser reutilizados.
- Código TOTP validado exclusivamente pelo backend.
- Códigos de recuperação poderão ser utilizados conforme o fluxo de segurança.
- Nenhum token de sessão definitiva será emitido antes da autenticação completa.

## 4.4. Política de autenticação por perfil

| Perfil | Autenticação |
| --- | --- |
| CUSTOMER sem 2FA | Senha |
| CUSTOMER com 2FA | Senha e TOTP |
| ADMIN | Senha e TOTP obrigatório |

ADMIN deverá configurar 2FA antes de acessar o painel administrativo.

## 4.5. Tokens e sessões

**Access Token:**

- Validade inicial: 10 minutos.
- JWT assinado.
- Payload mínimo.
- Validação de issuer, audience e assinatura.
- Identificador `jti`.
- Armazenamento em memória no frontend.

**Refresh Token:**

- Validade inicial: 7 dias.
- Rotação a cada renovação.
- Armazenamento somente do hash no banco.
- Cookie `HttpOnly`, `Secure` e `SameSite`.
- Revogação no logout.
- Detecção de reutilização de token anterior.

O frontend não deverá armazenar tokens de autenticação em `localStorage` ou `sessionStorage`.

## 4.6. Segundo fator

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| POST | `/auth/2fa/setup` | Iniciar configuração TOTP |
| POST | `/auth/2fa/confirm` | Confirmar configuração |
| POST | `/auth/2fa/verify` | Validar código |
| POST | `/auth/2fa/disable` | Desativar 2FA quando permitido |
| POST | `/auth/2fa/recovery-codes` | Gerar códigos de recuperação |
| POST | `/auth/2fa/recovery` | Utilizar código de recuperação |

Regras:

- CUSTOMER poderá habilitar 2FA opcionalmente.
- ADMIN deverá manter 2FA habilitado.
- ADMIN não poderá desativar seu 2FA.
- Segredo TOTP armazenado criptografado.
- Códigos de recuperação armazenados como hash.
- Códigos de recuperação de uso único.
- Rate limiting nas tentativas.
- Reautenticação para alterações de segurança.

## 4.7. Recuperação de senha e e-mail

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| POST | `/auth/password/forgot` | Solicitar recuperação |
| POST | `/auth/password/reset` | Redefinir senha |
| POST | `/auth/email/verify` | Verificar e-mail |
| POST | `/auth/email/resend-verification` | Reenviar verificação |

As respostas não deverão permitir enumeração de usuários.

## 5. Reautenticação contextual

## 5.1. Endpoint

`POST /api/v1/auth/reauthentication`

Requisição:

```json
{
  "password": "senha",
  "totpCode": "123456",
  "action": "ADMIN_TRANSACTION_REVERSAL"
}

```

Resposta:

```json
{
  "data": {
    "reauthenticationToken": "temporary-token",
    "expiresIn": 300,
    "action": "ADMIN_TRANSACTION_REVERSAL"
  }
}

```

## 5.2. Regras

A proposta aprovada estabelece:

- Validade de 5 minutos.
- Vínculo com usuário e sessão.
- Vínculo com ação específica.
- Uso único em operações críticas.
- Validação exclusiva no backend.
- Armazenamento em memória no frontend.
- Não substitui autenticação nem autorização.

O frontend enviará:

```http
X-Reauthentication-Token: <temporary-token>

```

A reautenticação será exigida conforme a política de risco e a criticidade da operação.

## 5.3. Matriz de reautenticação

| Operação | CUSTOMER | ADMIN |
| --- | --- | --- |
| Alterar senha | Senha atual | Senha atual |
| Alterar dados sensíveis | Senha atual | Senha e 2FA |
| Alterar segurança | Senha e 2FA se habilitado | Senha e 2FA |
| Alterar configurações críticas | Não permitido | Senha e 2FA |
| Estorno administrativo | Não permitido | Senha e 2FA |
| Bloquear ou encerrar cliente | Não permitido | Senha e 2FA |
| Reprocessar evento terminal | Não permitido | Senha e 2FA |
| Operações de cartão | Conforme política de risco | Conforme política de risco |

Operações administrativas críticas também exigirão justificativa e confirmação explícita.

Não haverá segunda aprovação administrativa.

## 6. Clientes

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| GET | `/customers/me` | Consultar perfil |
| PATCH | `/customers/me` | Atualizar dados permitidos |
| GET | `/customers/me/accounts` | Listar contas vinculadas |
| GET | `/customers/me/loans` | Consultar empréstimos |
| GET | `/customers/me/cards` | Consultar cartões |
| GET | `/customers/me/notifications` | Consultar notificações |

O cliente não poderá alterar diretamente seu status de negócio, perfil de autorização, identificadores internos ou informações de auditoria.

O status de identidade (`users.status`) será independente do status de negócio (`customer_profiles.status`).

A autorização deverá considerar ambos.

## 7. Contas bancárias

## 7.1. Endpoints

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| POST | `/accounts` | Solicitar abertura |
| GET | `/accounts` | Listar contas acessíveis |
| GET | `/accounts/{accountId}` | Consultar conta |
| GET | `/accounts/{accountId}/balance` | Consultar saldo |
| GET | `/accounts/{accountId}/statement` | Consultar extrato |
| POST | `/accounts/{accountId}/block` | Bloquear conta |
| POST | `/accounts/{accountId}/unblock` | Desbloquear conta |
| POST | `/accounts/{accountId}/close` | Solicitar encerramento |

O número de conta terá 9 dígitos e será gerado pelo backend. O UUID será o identificador interno.

## 7.2. Saldo

`GET /api/v1/accounts/{accountId}/balance`

Resposta:

```json
{
  "data": {
    "accountId": "uuid",
    "balance": "1500.00",
    "currency": "BRL",
    "updatedAt": "2026-11-12T14:30:00Z"
  }
}

```

O saldo será obtido da projeção consolidada do Ledger.

Operações pendentes não reservam saldo.

O saldo disponível corresponde ao valor efetivamente contabilizado.

## 7.3. Extrato

`GET /api/v1/accounts/{accountId}/statement`

Parâmetros:

| Parâmetro | Tipo | Obrigatório |
| --- | --- | --- |
| startDate | date | Não |
| endDate | date | Não |
| type | string | Não |
| status | string | Não |
| minAmount | decimal | Não |
| maxAmount | decimal | Não |
| description | string | Não |
| page | integer | Não |
| size | integer | Não |
| sort | string | Não |

Regras aprovadas:

- Período padrão: últimos 30 dias.
- Período máximo: 365 dias.
- Data inicial e final inclusivas.
- Fuso de interpretação: `America/Sao_Paulo`.
- Data inicial não pode ser posterior à final.
- Paginação padrão: 20 registros.
- Paginação máxima: 100 registros.
- Ordenação padrão: `createdAt,desc`.
- Campos de ordenação limitados aos campos permitidos.
- `minAmount` não poderá superar `maxAmount`.
- O cliente somente poderá consultar contas às quais tenha acesso autorizado.

Exemplo:

`GET /api/v1/accounts/{accountId}/statement?startDate=2026-10-01&endDate=2026-10-31&type=TRANSFER&page=0&size=20&sort=createdAt,desc`

## 7.4. Contas conjuntas

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| GET | `/accounts/{accountId}/holders` | Listar titulares |
| POST | `/accounts/{accountId}/invitations` | Convidar titular |
| GET | `/accounts/{accountId}/invitations` | Listar convites |
| POST | `/account-invitations/{invitationId}/accept` | Aceitar convite |
| POST | `/account-invitations/{invitationId}/cancel` | Cancelar convite |
| POST | `/accounts/{accountId}/leave` | Solicitar saída |
| POST | `/accounts/{accountId}/closure-consents` | Registrar consentimento |

Regras:

- Somente o destinatário autenticado poderá aceitar o convite.
- O destinatário não poderá participar de outra conta ativa.
- Convites terão validade limitada.
- Qualquer titular ativo poderá convidar outro cliente cadastrado.
- Titulares ativos terão direitos iguais.
- Nenhum titular poderá remover outro unilateralmente.
- Encerramento conjunto exige consentimento de todos os titulares ativos.
- Saída voluntária respeitará o número mínimo de titulares.
- Participações percentuais definidas deverão totalizar 100%.

## 8. Transações financeiras

## 8.1. Transferências

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| POST | `/transfers` | Criar transferência |
| GET | `/transfers/{transferId}` | Consultar transferência |
| GET | `/accounts/{accountId}/transfers` | Listar transferências |

Requisição:

```json
{
  "sourceAccountId": "uuid",
  "destinationAccountId": "uuid",
  "amount": "250.00",
  "description": "Transferência entre contas"
}

```

Cabeçalho obrigatório:

```http
Idempotency-Key: <chave-única>

```

Regras:

- Transferências exclusivamente entre contas MyBank.
- Validação de titularidade e autorização.
- Validação de saldo e estado das contas.
- Processamento síncrono.
- Operação atômica.
- Ledger com débitos e créditos correspondentes.
- Bloqueios determinísticos de contas.
- Proibição de saldo negativo.
- Não alteração ou exclusão de transferências contabilizadas.

## 8.2. Depósitos simulados

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| POST | `/deposits` | Solicitar depósito |
| GET | `/deposits/{depositId}` | Consultar depósito |
| GET | `/accounts/{accountId}/deposits` | Listar depósitos |

Requisição:

```json
{
  "accountId": "uuid",
  "amount": "500.00"
}

```

O depósito será simulado e terá contrapartida em conta contábil de compensação.

## 8.3. Saques simulados

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| POST | `/withdrawals` | Solicitar saque |
| GET | `/withdrawals/{withdrawalId}` | Consultar saque |
| GET | `/accounts/{accountId}/withdrawals` | Listar saques |

Requisição:

```json
{
  "accountId": "uuid",
  "amount": "100.00"
}

```

O saque será simulado, com contrapartida contábil correspondente.

## 8.4. Processamento financeiro

Transferências, depósitos, saques, compras simuladas, pagamentos de serviços, pagamentos de parcelas e estornos serão processados sincronamente na primeira versão.

A resposta somente poderá indicar conclusão quando a contabilização tiver sido confirmada.

O backend deverá garantir:

- ACID.
- Idempotência.
- Validação de estado.
- Revalidação de saldo.
- Bloqueios determinísticos.
- Partidas dobradas.
- Débitos iguais a créditos.
- Atualização atômica da projeção de saldo.
- Imutabilidade do Ledger.

## 9. Pagamentos

## 9.1. Endpoints

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| GET | `/payment-providers` | Listar prestadores simulados |
| POST | `/payments` | Criar pagamento |
| GET | `/payments/{paymentId}` | Consultar pagamento |
| GET | `/accounts/{accountId}/payments` | Listar pagamentos |
| POST | `/payments/{paymentId}/cancel` | Cancelar pagamento elegível |

Requisição:

```json
{
  "accountId": "uuid",
  "providerId": "uuid",
  "amount": "180.00",
  "description": "Conta de energia",
  "scheduledAt": null
}

```

Categorias:

- Água.
- Energia.
- Internet.
- Telefonia.
- Gás.
- Outros serviços simulados.

Regras:

- Não haverá boletos reais.
- Não haverá integração externa.
- Não haverá pagamentos entre clientes.
- Pagamento imediato será processado sincronamente.
- Agendamento será registrado sincronamente e executado assincronamente na data programada.
- O processamento agendado deverá revalidar saldo, estado da conta e demais condições.
- Pagamentos agendados poderão ser cancelados antes do processamento.
- Pagamentos em processamento não poderão ser cancelados diretamente.

## 10. Empréstimos

## 10.1. Endpoints do cliente

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| POST | `/loans/simulations` | Simular empréstimo |
| POST | `/loans` | Solicitar empréstimo |
| GET | `/loans` | Listar empréstimos |
| GET | `/loans/{loanId}` | Consultar empréstimo |
| GET | `/loans/{loanId}/installments` | Consultar parcelas |
| GET | `/loans/{loanId}/contract` | Consultar contrato |
| POST | `/loans/{loanId}/cancel` | Cancelar solicitação elegível |
| GET | `/loans/{loanId}/installments/{installmentId}` | Consultar parcela |
| POST | `/loans/{loanId}/installments/{installmentId}/payments` | Quitar parcela |
| GET | `/loans/{loanId}/installments/{installmentId}/payments` | Consultar pagamentos |

## 10.2. Simulação

`POST /api/v1/loans/simulations`

Requisição:

```json
{
  "principalAmount": "10000.00",
  "installmentsCount": 12
}

```

A resposta deverá apresentar:

- Valor solicitado.
- Quantidade de parcelas.
- Taxa mensal.
- Valor estimado da parcela.
- Valor total estimado.
- Total estimado de juros.
- Sistema Price.

A simulação não representa aprovação ou contratação.

## 10.3. Solicitação

`POST /api/v1/loans`

Requisição:

```json
{
  "principalAmount": "10000.00",
  "installmentsCount": 12,
  "declaredMonthlyIncome": "5000.00"
}

```

A análise considerará:

- Renda declarada.
- Empréstimos ativos.
- Parcelas comprometidas.
- Valor solicitado.
- Prazo.
- Taxa aplicável.
- Comprometimento máximo de renda de 30%.

O backend deverá controlar concorrência na análise e aprovação.

## 10.4. Administração

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| GET | `/admin/loans` | Listar solicitações |
| GET | `/admin/loans/{loanId}` | Consultar solicitação |
| POST | `/admin/loans/{loanId}/review` | Iniciar análise |
| POST | `/admin/loans/{loanId}/approve` | Aprovar |
| POST | `/admin/loans/{loanId}/reject` | Rejeitar |
| POST | `/admin/loans/{loanId}/contract` | Formalizar contratação |

A aprovação e rejeição exigirão justificativa e auditoria.

As condições aprovadas serão preservadas no contrato.

## 10.5. Pagamento integral de parcelas

**Regra aprovada:** na primeira versão, pagamentos parciais não serão permitidos.

Endpoint:

`POST /api/v1/loans/{loanId}/installments/{installmentId}/payments`

Requisição:

```json
{
  "paymentMethod": "SIMULATED_SETTLEMENT"
}

```

O backend determinará o valor integral devido.

Resposta:

```json
{
  "data": {
    "paymentId": "uuid",
    "loanId": "uuid",
    "installmentId": "uuid",
    "amount": "850.00",
    "status": "COMPLETED",
    "paymentMethod": "SIMULATED_SETTLEMENT",
    "paidAt": "2026-11-12T14:30:00Z"
  }
}

```

Regras:

- Parcela deverá estar `PENDING` ou `OVERDUE`.
- Parcela quitada não poderá ser paga novamente.
- Valor será calculado no backend.
- Pagamento será integral.
- Operação será síncrona e idempotente.
- Parcela passará para `PAID` após contabilização bem-sucedida.
- A operação será independente da existência de conta bancária ativa.
- O pagamento utilizará liquidação simulada.
- A transação poderá ter `accountId` nulo, mantendo a identificação do cliente responsável.

O encerramento da conta bancária não extingue a obrigação do empréstimo.

## 11. Cartões virtuais

## 11.1. Endpoints

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| POST | `/cards` | Criar cartão virtual |
| GET | `/cards` | Listar cartões |
| GET | `/cards/{cardId}` | Consultar cartão |
| POST | `/cards/{cardId}/block` | Bloquear |
| POST | `/cards/{cardId}/unblock` | Desbloquear |
| POST | `/cards/{cardId}/cancel` | Cancelar |
| GET | `/cards/{cardId}/transactions` | Consultar compras |
| POST | `/cards/{cardId}/purchases` | Registrar compra simulada |
| GET | `/card-transactions/{transactionId}` | Consultar compra |
| POST | `/card-transactions/{transactionId}/reverse` | Estornar compra elegível |

Regras:

- Cartões exclusivamente virtuais e fictícios.
- Sem bandeira ou cartão físico.
- Sem compras reais.
- Limite representa limite de gasto, não crédito.
- Compra debita o saldo bancário e consome limite.
- Estorno recompõe saldo e limite.
- Cartão cancelado não poderá ser reativado.
- Não armazenar CVV ou dados equivalentes.
- Número demonstrativo não poderá representar PAN real.

## 12. Estornos administrativos

## 12.1. Endpoint

`POST /api/v1/admin/transactions/{transactionId}/reversals`

Requisição:

```json
{
  "reason": "Correção de operação processada indevidamente"
}

```

Cabeçalho obrigatório:

```http
Idempotency-Key: <chave-única>
X-Reauthentication-Token: <temporary-token>

```

## 12.2. Política

Regras aprovadas:

- Estorno integral na primeira versão.
- Uma reversão integral por transação original.
- Exclusivo de ADMIN autorizado.
- Justificativa obrigatória.
- Confirmação adicional.
- Reautenticação com senha e TOTP.
- Auditoria obrigatória.
- Operação atômica e idempotente.
- Não estornar estorno.
- Não alterar registros originais.
- Gerar nova transação e novos lançamentos de Ledger.
- Validar efeitos financeiros para impedir saldo negativo.
- Respeitar limites administrativos configuráveis.
- Não haverá segunda aprovação.

## 12.3. Categorias elegíveis propostas e aprovadas

| Categoria | Tratamento |
| --- | --- |
| Transferência | Elegível, respeitando condições financeiras |
| Depósito simulado | Elegível |
| Saque simulado | Elegível |
| Pagamento simulado | Elegível |
| Compra simulada com cartão | Elegível, respeitando política do cartão |

As operações de empréstimo não utilizarão o endpoint genérico de estorno. Correções relacionadas a desembolsos, parcelas e contratos deverão seguir fluxos específicos.

## 12.4. Limites configuráveis

A política administrativa deverá permitir controlar:

- Valor máximo por estorno.
- Limite acumulado por período.
- Quantidade máxima de operações por período.
- Categorias elegíveis.
- Idade máxima da transação original, quando aplicável.
- Condições de saldo e compensação.

Os limites numéricos não serão fixados diretamente nos contratos ou no código.

A alteração dos parâmetros deverá ser autorizada, justificada e auditada.

## 12.5. Fluxo de execução

1. Validar ADMIN.
2. Validar 2FA.
3. Validar reautenticação.
4. Validar justificativa.
5. Validar elegibilidade da transação.
6. Verificar reversão anterior.
7. Validar limites administrativos.
8. Validar efeitos financeiros.
9. Validar idempotência.
10. Criar transação compensatória.
11. Registrar lançamentos no Ledger.
12. Registrar auditoria.
13. Confirmar atomicamente.

Se uma validação falhar, nenhuma alteração financeira deverá ser contabilizada.

## 13. Notificações

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| GET | `/notifications` | Listar notificações |
| GET | `/notifications/{notificationId}` | Consultar notificação |
| PATCH | `/notifications/{notificationId}/read` | Marcar como lida |
| POST | `/notifications/read-all` | Marcar todas como lidas |
| GET | `/notifications/preferences` | Consultar preferências |
| PATCH | `/notifications/preferences` | Atualizar preferências |

Notificações serão internas ao sistema.

Não haverá integração com provedores reais de e-mail, SMS ou push na primeira versão.

O processamento de notificações será assíncrono.

Falhas deverão ser registradas, com tentativas limitadas, backoff progressivo e estado terminal de falha quando o limite for atingido.

## 14. Administração

Todos os endpoints administrativos exigirão:

- Perfil ADMIN.
- Usuário autenticado e sessão administrativa com 2FA concluído.
- Autorização por perfil validada pelo backend em cada operação.
- Reautenticação quando exigida pela criticidade.
- Justificativa e auditoria nas ações críticas.

O esquema Bearer representa autenticação e não substitui a autorização por perfil nem a exigência de 2FA. A consulta `GET /admin/loans` exige perfil ADMIN e sessão com 2FA concluído, deve ser registrada na auditoria e não exige reautenticação contextual.

## 14.1. Clientes

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| GET | `/admin/customers` | Listar clientes |
| GET | `/admin/customers/{customerId}` | Consultar cliente |
| POST | `/admin/customers/{customerId}/block` | Bloquear |
| POST | `/admin/customers/{customerId}/unblock` | Desbloquear |
| POST | `/admin/customers/{customerId}/suspend` | Suspender |
| POST | `/admin/customers/{customerId}/close` | Encerrar |

## 14.2. Contas

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| GET | `/admin/accounts` | Listar contas |
| GET | `/admin/accounts/{accountId}` | Consultar conta |
| POST | `/admin/accounts/{accountId}/block` | Bloquear |
| POST | `/admin/accounts/{accountId}/unblock` | Desbloquear |
| POST | `/admin/accounts/{accountId}/suspend` | Suspender |
| POST | `/admin/accounts/{accountId}/close` | Encerrar |

As transições deverão respeitar as regras do domínio.

## 14.3. Administradores

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| GET | `/admin/administrators` | Listar administradores |
| POST | `/admin/administrators/invitations` | Convidar administrador |
| GET | `/admin/administrators/invitations` | Consultar convites |
| POST | `/admin/administrators/invitations/{invitationId}/cancel` | Cancelar convite |
| POST | `/admin/administrators/{userId}/block` | Bloquear administrador |
| POST | `/admin/administrators/{userId}/unblock` | Reativar administrador |

Regras:

- Somente CUSTOMER e ADMIN como perfis do sistema.
- Todos os administradores terão o mesmo nível de acesso.
- Convites terão validade limitada e uso único.
- Destinatário definirá a própria senha.
- Não será permitido desativar o último ADMIN ativo.
- Não haverá segunda aprovação administrativa.
- Ações críticas exigirão justificativa e auditoria.

## 14.4. Configurações

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| GET | `/admin/settings` | Consultar configurações |
| GET | `/admin/settings/{key}` | Consultar configuração |
| PATCH | `/admin/settings/{key}` | Alterar configuração |

Configurações críticas exigirão reautenticação, justificativa e auditoria.

## 15. Auditoria e privacidade

## 15.1. Auditoria

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| GET | `/admin/audit-events` | Consultar eventos |
| GET | `/admin/audit-events/{eventId}` | Consultar evento específico |

Filtros:

- Usuário responsável.
- Tipo de evento.
- Recurso afetado.
- Período.
- Resultado.
- Identificador de correlação.

A consulta de dados sensíveis também será auditada.

Eventos de auditoria não poderão ser alterados ou removidos por operações administrativas comuns.

## 15.2. Privacidade

| Método | Endpoint | Finalidade |
| --- | --- | --- |
| POST | `/privacy/requests` | Registrar solicitação |
| GET | `/privacy/requests` | Listar solicitações |
| GET | `/privacy/requests/{requestId}` | Consultar solicitação |

O tratamento deverá observar LGPD, finalidade, base legal e obrigações de retenção.

Não será permitida exclusão indiscriminada de transações, Ledger ou auditoria.

## 16. Processamento síncrono e assíncrono

## 16.1. Operações síncronas

| Operação | Processamento |
| --- | --- |
| Transferência | Síncrono |
| Depósito simulado | Síncrono |
| Saque simulado | Síncrono |
| Compra simulada | Síncrono |
| Pagamento imediato | Síncrono |
| Pagamento de parcela | Síncrono |
| Estorno administrativo | Síncrono |
| Criação de cartão | Síncrono |
| Bloqueio e desbloqueio de cartão | Síncrono |

A resposta somente poderá indicar conclusão após a confirmação da contabilização.

## 16.2. Operações assíncronas

| Operação | Processamento |
| --- | --- |
| Notificações | Assíncrono |
| Eventos Outbox | Assíncrono |
| Comprovantes auxiliares | Assíncrono |
| Pagamentos agendados | Assíncrono na data programada |
| Processamentos administrativos demorados | Assíncrono quando necessário |

## 16.3. HTTP 202

HTTP 202 será utilizado somente quando uma operação tiver sido aceita e ainda depender de processamento posterior.

Exemplo:

```json
{
  "data": {
    "transactionId": "uuid",
    "status": "PROCESSING",
    "statusUrl": "/api/v1/transactions/uuid"
  }
}

```

O cliente deverá consultar o recurso para acompanhar o processamento.

A criação de um agendamento poderá retornar HTTP 201 quando o recurso agendado tiver sido efetivamente criado, mesmo que sua execução financeira aconteça posteriormente.

O backend não poderá apresentar uma operação como `COMPLETED` antes da contabilização.

## 17. Idempotência e concorrência

## 17.1. Idempotência

Operações financeiras deverão exigir `Idempotency-Key`.

O backend deverá associar:

- Chave.
- Usuário responsável.
- Tipo de operação.
- Dados da requisição.
- Resultado.
- Identificador da operação criada.

Regras:

- Mesma chave e mesmos dados: retornar resultado original.
- Mesma chave e dados diferentes: HTTP 409.
- Chaves não poderão ser compartilhadas entre contextos incompatíveis.
- Operações concluídas não poderão ser executadas novamente por repetição de requisição.

## 17.2. Concorrência

O backend deverá:

- Revalidar saldo durante o processamento.
- Aplicar bloqueios determinísticos.
- Evitar deadlocks.
- Garantir atomicidade.
- Controlar concorrência em aprovação de empréstimos.
- Evitar pagamento duplicado de parcela.
- Evitar estornos duplicados.
- Atualizar a projeção de saldo atomicamente.

## 18. Paginação, filtros e ordenação

Listagens deverão utilizar paginação padronizada.

Parâmetros gerais:

- `page`: página iniciada em zero.
- `size`: quantidade de registros.
- `sort`: campo e direção.

Padrões:

- Página inicial: 0.
- Tamanho padrão: 20.
- Tamanho máximo: 100.
- Ordenação validada pelo backend.

Resposta:

```json
{
  "data": [],
  "meta": {
    "page": 0,
    "size": 20,
    "totalElements": 120,
    "totalPages": 6,
    "correlationId": "uuid"
  }
}

```

## 19. Máquinas de estados

A API deverá respeitar as máquinas de estados aprovadas.

## 19.1. Transações

`PENDING -> PROCESSING -> COMPLETED | FAILED | REJECTED`

`PENDING -> CANCELLED`

Estados finais não poderão ser reabertos.

## 19.2. Pagamentos

`PENDING -> SCHEDULED | PROCESSING | CANCELLED`

`SCHEDULED -> PROCESSING | CANCELLED`

`PROCESSING -> COMPLETED | FAILED | REJECTED`

## 19.3. Empréstimos

`REQUESTED -> UNDER_REVIEW | REJECTED | CANCELLED`

`UNDER_REVIEW -> APPROVED | REJECTED`

`APPROVED -> CONTRACTED | CANCELLED`

`CONTRACTED -> ACTIVE`

`ACTIVE -> PAID_OFF`

## 19.4. Parcelas

- `PENDING`
- `PAID`
- `OVERDUE`
- `CANCELLED`

`OVERDUE -> PAID`

## 19.5. Convites

`PENDING -> ACCEPTED | EXPIRED | CANCELLED`

## 19.6. Cartões

- `ACTIVE`
- `BLOCKED`
- `CANCELLED`

Cartões bloqueados poderão ser reativados. Cartões cancelados não poderão ser reativados.

## 19.7. Clientes

- `PENDING`
- `ACTIVE`
- `BLOCKED`
- `SUSPENDED`
- `CLOSED`

## 19.8. Contas

- `PENDING`
- `ACTIVE`
- `BLOCKED`
- `SUSPENDED`
- `CLOSED`

As alterações de estado ocorrerão por operações específicas, com validação no domínio e histórico persistido.

## 20. Segurança dos contratos

A API deverá aplicar:

1. Autenticação nos endpoints protegidos.
2. Autorização por perfil e contexto.
3. Validação de titularidade.
4. Validação dos estados de usuário, cliente e conta.
5. Rate limiting.
6. Validação de entradas.
7. Proteção contra enumeração.
8. Proteção contra XSS.
9. Proteção CSRF quando aplicável ao uso de cookies.
10. CSP no frontend.
11. Tratamento seguro de erros.
12. Auditoria de operações críticas.
13. Reautenticação contextual.
14. 2FA conforme política.
15. Idempotência.
16. Proteção de dados pessoais.
17. Validação de origem e políticas de CORS.
18. Gestão segura de segredos.

O frontend não será considerado uma fronteira de segurança.

Ocultar menus, botões ou rotas não substitui autorização no backend.

## 21. Outbox e eventos

A Outbox será utilizada para publicação confiável de eventos internos.

Eventos poderão ser utilizados para:

- Notificações.
- Atualizações de estado.
- Comprovantes auxiliares.
- Processamentos posteriores.

Regras:

- Tentativas limitadas.
- Backoff progressivo.
- Histórico de tentativas.
- Recuperação de processamento abandonado.
- Estado terminal de falha.
- Idempotência dos consumidores.
- Reprocessamento manual controlado.
- Justificativa e auditoria no reprocessamento.
- Preservação do histórico original.

Estados documentados da Outbox: `PENDING` indica evento aguardando processamento; `PROCESSING`, execução em andamento; `PROCESSED`, processamento concluído com sucesso; `FAILED`, falha terminal que pode ser elegível a reprocessamento manual após validações e autorizações. `DEAD_LETTER` é somente mecanismo operacional de encaminhamento ou armazenamento e não constitui estado do domínio. O reprocessamento inicia novo ciclo sem apagar tentativas anteriores.

O reprocessamento deverá iniciar um novo ciclo de execução sem apagar as tentativas anteriores.

O payload dos eventos não deverá conter dados sensíveis desnecessários.

## 22. Arquitetura de implementação

A implementação dos contratos deverá respeitar a Arquitetura Hexagonal e a organização modular.

## 22.1. Adaptadores de entrada REST

Responsabilidades:

- Receber requisições HTTP.
- Validar estrutura e formato.
- Converter DTOs.
- Encaminhar comandos e consultas.
- Converter resultados em respostas HTTP.

## 22.2. Camada de aplicação

Responsabilidades:

- Orquestrar casos de uso.
- Aplicar autorização contextual.
- Controlar transações.
- Coordenar portas e serviços.
- Orquestrar operações de negócio.

## 22.3. Domínio

Responsabilidades:

- Aplicar regras de negócio.
- Validar transições.
- Proteger invariantes financeiras.
- Controlar regras de titularidade.
- Impedir operações inválidas.

## 22.4. Adaptadores de saída

Responsabilidades:

- Persistência PostgreSQL.
- Integração com Ledger.
- Outbox.
- Serviços auxiliares.

Controllers não deverão conter regras de negócio nem acessar diretamente repositórios JPA.

## 23. Organização modular dos endpoints

| Módulo | Responsabilidade |
| --- | --- |
| Identity | Autenticação, sessões, senha e 2FA |
| Customer | Perfil e dados do cliente |
| Account | Contas, titulares e convites |
| Transaction | Transferências, depósitos, saques e estornos |
| Ledger | Contabilização e consultas internas |
| Payment | Pagamentos simulados |
| Loan | Empréstimos e parcelas |
| Card | Cartões e compras simuladas |
| Notification | Notificações e preferências |
| Admin | Operações administrativas |
| Audit | Auditoria |
| Privacy | Solicitações de privacidade |

O Ledger não deverá oferecer endpoints públicos para alteração direta de lançamentos.

## 24. Estratégia de testes dos contratos

A implementação futura deverá contemplar:

- Respostas HTTP.
- Validação de DTOs.
- Campos obrigatórios.
- Formatos e tipos.
- Autenticação.
- Autorização.
- Titularidade de contas.
- Isolamento entre clientes.
- Transições de estados.
- Idempotência.
- Concorrência financeira.
- Saldo insuficiente.
- Integridade do Ledger.
- Estornos administrativos.
- Pagamento integral de parcelas.
- Pagamento após encerramento de conta.
- Desafio de 2FA.
- Reautenticação contextual.
- Expiração e reutilização de tokens.
- Rate limiting.
- CSRF e XSS.
- Falhas e reprocessamento de eventos.
- Compatibilidade dos contratos OpenAPI.

Ferramentas previstas:

- JUnit 5.
- Mockito.
- Spring Boot Test.
- Testcontainers.
- ArchUnit.
- Testes de integração HTTP.
- Validação OpenAPI.

## 25. Pendências e limites da especificação

Esta versão incorpora os seis fechamentos aprovados.

Os seguintes detalhes ainda deverão ser tratados em documentos complementares, sem bloquear a consolidação dos contratos:

1. Valores numéricos efetivos dos limites administrativos de estorno, conforme a política previamente aprovada.
2. Regras operacionais detalhadas para categorias de estorno e situações excepcionais.
3. Detalhamento dos esquemas completos de todos os DTOs na especificação OpenAPI.
4. Catálogo definitivo de códigos de erro.
5. Exemplos completos de todas as respostas e cenários de falha.
6. Matriz endpoint por endpoint de autenticação, perfil, titularidade, 2FA e reautenticação.

Esses itens deverão ser detalhados sem contradizer decisões já aprovadas.

## 26. Critérios de aceite

A especificação será considerada concluída quando:

- Todos os módulos possuírem endpoints documentados.
- Métodos HTTP estiverem definidos.
- Estruturas de requisição e resposta estiverem padronizadas.
- Códigos HTTP estiverem definidos.
- Autenticação e autorização estiverem especificadas.
- Fluxos de 2FA e reautenticação estiverem documentados.
- Operações financeiras respeitarem Ledger e idempotência.
- Pagamentos de parcelas forem integrais na primeira versão.
- Operações síncronas e assíncronas estiverem diferenciadas.
- Regras de extrato estiverem consolidadas.
- Estornos respeitarem a política aprovada.
- Máquinas de estados estiverem refletidas nos contratos.
- Segurança e auditoria estiverem contempladas.
- Não houver conflito com a arquitetura, modelagem e especificação funcional aprovadas.
- A especificação puder ser formalizada em OpenAPI 3.1.1.

---

**Fim do documento — MyBank API REST v1.1**

## 27. Decisões complementares aprovadas — reprocessamento da Outbox

## Endpoint

`POST /api/v1/admin/outbox-events/{eventId}/reprocess`

O comando é exclusivo de ADMIN e exige autenticação, 2FA, `X-Reauthentication-Token`, justificativa, confirmação adicional, `Idempotency-Key` e auditoria. O corpo contém `reason` (10 a 500 caracteres) e `confirmation: true`. A confirmação é incluída porque a decisão complementar exige confirmação adicional, e o exemplo da consolidação complementar v1.1 foi harmonizado para incluí-la.

A solicitação elegível retorna `202 Accepted`, com `eventId`, estado inicial `PENDING`, mensagem e `statusUrl` apontando para `/api/v1/admin/outbox-events/{eventId}`. O recurso de consulta é protegido para ADMIN e apresenta o estado e o histórico de tentativas conforme a política de acesso. A aceitação não apaga tentativas anteriores nem altera o conteúdo original do evento.

`PROCESSED` representa processamento concluído com sucesso. Somente eventos elegíveis com estado `FAILED` podem ser reprocessados, após validação pelo backend. `DEAD_LETTER` designa o encaminhamento ou armazenamento operacional de eventos esgotados e não é estado alternativo nem critério autônomo de elegibilidade.

O schema de `attempts` preserva o histórico em uma lista de objetos, mas a documentação aprovada não define os campos de cada tentativa; a estrutura detalhada permanece pendente de referência aprovada.
