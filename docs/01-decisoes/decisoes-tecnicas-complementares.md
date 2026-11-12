# MyBank — Consolidação das Decisões Técnicas Complementares

| Campo | Valor |
| --- | --- |
| Identificador | MB-DEC-001 |
| Classe | Documento oficial da biblioteca MyBank |
| Original preservado | [MyBank — Consolidação das Decisões Técnicas Complementares.md](../99-fontes-originais/MyBank%20—%20Consolidação%20das%20Decisões%20Técnicas%20Complementares.md) |
| Versão | 1.1 |
| Status | Consolidado — decisões aprovadas |
| Finalidade | Reunir decisões técnicas complementares e estabelecer orientações para atualização e auditoria dos documentos do projeto. |

---

## Sumário

- [1. Identificação dos clientes](#1-identificacao-dos-clientes)
- [2. Proteção do CPF](#2-protecao-do-cpf)
- [3. Snapshot contratual dos empréstimos](#3-snapshot-contratual-dos-emprestimos)
- [4. Estornos administrativos](#4-estornos-administrativos)
- [5. Reprocessamento da Outbox](#5-reprocessamento-da-outbox)
- [6. Atualizações documentais obrigatórias](#6-atualizacoes-documentais-obrigatorias)
- [7. Auditoria técnica final](#7-auditoria-tecnica-final)
- [8. Restrições permanentes](#8-restricoes-permanentes)
- [9. Critério de conclusão](#9-criterio-de-conclusao)

## 1. Identificação dos clientes

- CPF será o único documento de identificação aceito na primeira versão.
- CPF obrigatório no cadastro de clientes.
- Validação de formato e dígitos verificadores.
- Unicidade de CPF por cliente.
- Proibição de duplicidade, garantida pela aplicação e pelo banco de dados.

## 2. Proteção do CPF

### 2.1. Criptografia e pesquisa

- Criptografia AES-256-GCM para proteção do CPF armazenado.
- HMAC-SHA-256 para pesquisa e verificação de unicidade.
- Chave HMAC independente da chave de criptografia.
- Chaves mantidas fora do banco de dados e do código-fonte.
- Versionamento das chaves para permitir rotação controlada.
- Proteção dos registros existentes durante a rotação.

### 2.2. Representação persistida

A implementação deverá prever:

- CPF criptografado.
- Nonce utilizado na operação criptográfica.
- Tag de autenticação.
- Identificador ou versão da chave de criptografia.
- HMAC utilizado para pesquisa e unicidade.
- Identificador ou versão da chave HMAC, quando necessário para suportar rotação.

A representação definitiva deverá ser documentada no dicionário de dados antes da implementação.

### 2.3. Restrições de segurança

- Nunca registrar CPF em logs.
- Nunca registrar conteúdo criptografado em logs.
- Nunca registrar HMAC do CPF em logs.
- Não armazenar chaves criptográficas no banco de dados.
- Não armazenar chaves no código-fonte.
- Restringir o acesso aos mecanismos de criptografia e pesquisa.

A rotação deverá considerar a capacidade de localizar registros existentes, verificar unicidade e atualizar dados protegidos sem perda de informação.

## 3. Snapshot contratual dos empréstimos

Cada empréstimo contratado deverá preservar as condições efetivamente aprovadas.

### 3.1. Informações obrigatórias

- Valor principal aprovado.
- Taxa de juros mensal.
- Sistema de amortização PRICE.
- Quantidade de parcelas.
- Valor de cada parcela.
- Data da primeira parcela.
- Datas de vencimento.
- Total de juros.
- Valor total contratado.
- Data da aprovação.
- Data da contratação.
- Versão das condições aplicadas.

### 3.2. Regras de imutabilidade

- As condições contratadas não poderão ser alteradas retroativamente.
- Alterações futuras nas configurações gerais não modificarão contratos existentes.
- As parcelas terão registros próprios vinculados ao contrato.
- O snapshot deverá permanecer disponível após a quitação.
- A representação persistida deverá permitir auditoria e reconstrução das condições contratadas.

A implementação deverá definir os mecanismos técnicos de proteção contra alterações indevidas, incluindo restrições de aplicação, banco de dados e permissões.

Não serão inventadas tarifas, seguros ou valores de CET.

## 4. Estornos administrativos

Os estornos da primeira versão serão integrais.

### 4.1. Autorização e segurança

- Execução exclusiva por ADMIN.
- 2FA obrigatório.
- Reautenticação.
- Justificativa obrigatória.
- Confirmação adicional.
- Registro de auditoria.
- Idempotência.
- Respeito às permissões e aos limites configurados.

### 4.2. Regras financeiras

- Uma reversão integral por transação original.
- Proibição de estornar um estorno.
- Proibição de produzir saldo negativo.
- Somente transações concluídas e elegíveis poderão ser estornadas.
- O Ledger original não poderá ser alterado ou apagado.
- A reversão deverá gerar novos lançamentos compensatórios.
- Empréstimos e pagamentos de parcelas permanecerão em fluxos específicos.

### 4.3. Limites configuráveis

O sistema deverá permitir configurar:

- Valor máximo por estorno.
- Valor acumulado máximo por período.
- Quantidade máxima de estornos por período.
- Categorias elegíveis.
- Idade máxima da transação.

Os valores numéricos serão definidos posteriormente, sem introdução de limites arbitrários.

Se qualquer limite obrigatório não estiver configurado, o estorno ficará indisponível.

As alterações nas configurações dos limites deverão ser auditadas.

## 5. Reprocessamento da Outbox

Eventos que atingirem o estado terminal `FAILED` poderão ser submetidos a reprocessamento manual, desde que atendam aos critérios de elegibilidade.

### 5.1. Requisitos

- Operação exclusiva de ADMIN.
- 2FA obrigatório.
- Reautenticação.
- Justificativa obrigatória.
- Confirmação adicional.
- Auditoria.
- Idempotência.
- Preservação do histórico de tentativas.
- Controle de concorrência.
- Respeito aos limites configurados.
- Proibição de reprocessar eventos definitivamente inválidos.

### 5.2. Processamento

- Somente eventos elegíveis em estado terminal `FAILED` poderão ser reprocessados.
- Eventos inválidos definitivamente não poderão ser reprocessados.
- Novas tentativas deverão preservar as tentativas anteriores.
- O histórico não poderá ser apagado.
- Consumidores deverão garantir idempotência.
- Novas falhas seguirão novamente a política de retries e backoff progressivo.
- O evento poderá retornar ao fluxo de processamento conforme as regras de estado definidas.
- Falhas posteriores poderão resultar novamente em estado terminal `FAILED`.

O reprocessamento deverá impedir execuções concorrentes incompatíveis e manter rastreabilidade completa.

### 5.3. Contrato REST

**Endpoint:**

`POST /api/v1/admin/outbox-events/{eventId}/reprocess`

**Cabeçalhos:**

- `Authorization: Bearer`
- `X-Reauthentication-Token`
- `Idempotency-Key`
- `Content-Type: application/json`

**Corpo da requisição:**

```json
{
  "reason": "Justificativa obrigatória para o reprocessamento",
  "confirmation": true
}

```

**Resposta:**

- HTTP `202 Accepted`.
- Identificador do evento.
- Estado inicial do reprocessamento: `PENDING`.
- Mensagem informativa.
- `statusUrl` para acompanhamento do processamento.

Exemplo:

```json
{
  "eventId": "UUID",
  "status": "PENDING",
  "message": "Reprocessamento solicitado.",
  "statusUrl": "/api/v1/admin/outbox-events/UUID"
}

```

A URL de acompanhamento deverá utilizar o contrato administrativo de consulta de eventos da Outbox.

### 5.4. Autorização

O campo `confirmation` deverá ser `true` como confirmação explícita da operação. O corpo exige `reason` e `confirmation`.

O endpoint deverá validar:

- Perfil ADMIN.
- Sessão administrativa autenticada com 2FA.
- Reautenticação válida e vinculada à ação.
- Justificativa não vazia.
- Evento em estado terminal `FAILED`.
- Elegibilidade do evento.
- Ausência de reprocessamento concorrente incompatível.
- Limites operacionais configurados.
- Idempotência da solicitação.

Toda solicitação deverá gerar registro de auditoria.

## 6. Atualizações documentais obrigatórias

As decisões deverão ser incorporadas e conferidas nos seguintes documentos:

| Documento | Atualização necessária |
| --- | --- |
| `AGENTS.md` | Regras permanentes de segurança, arquitetura e desenvolvimento |
| `ROADMAP.md` | Estado das etapas documentais e pendências |
| ADRs | Proteção de dados e decisões técnicas complementares |
| Especificação funcional | CPF, empréstimos, estornos e Outbox |
| Arquitetura | Proteção criptográfica, fluxos de reprocessamento e responsabilidades |
| DER | Campos de CPF protegido, snapshot e histórico |
| Dicionário de dados | Definição dos novos campos, tipos e restrições |
| Contratos REST | Endpoint de reprocessamento e resposta assíncrona |
| OpenAPI 3.1.1 | Schemas, parâmetros, respostas, segurança e exemplos |
| Especificação Frontend | Telas administrativas e apresentação dos estados da Outbox |

As atualizações deverão preservar as decisões anteriormente aprovadas.

## 7. Auditoria técnica final

### Complemento aprovado após revisão semântica do OpenAPI

- `PROCESSED` representa processamento concluído com sucesso na Outbox.
- `FAILED` é o estado terminal de falha elegível a reprocessamento manual, condicionado às validações e autorizações aprovadas.
- `DEAD_LETTER` é mecanismo operacional de encaminhamento ou armazenamento de eventos com falha, não um estado alternativo do domínio.
- Todas as operações sob `/admin/**` exigem usuário autenticado com perfil ADMIN, sessão administrativa com 2FA concluído e autorização validada pelo backend. Bearer representa autenticação e não substitui autorização por perfil.
- `GET /admin/loans` exige ADMIN e 2FA, gera registro de auditoria e não exige reautenticação contextual.
- A estrutura de campos dos itens do histórico `attempts` não está definida nos documentos aprovados; permanece genérica até existir referência aprovada.

Antes do encerramento da consolidação documental, deverão ser realizadas as seguintes verificações:

1. Conferir a representação persistida do CPF criptografado e seus metadados.
2. Definir o procedimento de rotação das chaves e a manutenção da capacidade de pesquisa e unicidade.
3. Conferir a imutabilidade do snapshot contratual dos empréstimos.
4. Conferir a correspondência entre o endpoint de reprocessamento e a máquina de estados da Outbox.
5. Conferir a idempotência e o controle de concorrência do reprocessamento.
6. Conferir a presença de `statusUrl` nas respostas assíncronas aplicáveis.
7. Gerar e validar sintaticamente a especificação OpenAPI 3.1.1.
8. Verificar a correspondência entre requisitos funcionais, arquitetura, DER, dicionário de dados, contratos REST, OpenAPI e frontend.
9. Identificar conflitos, omissões, duplicidades e decisões divergentes.
10. Registrar pendências que dependam de decisões adicionais, sem inventar soluções ou parâmetros.

## 8. Restrições permanentes

- Não alterar decisões anteriormente aprovadas sem justificativa e autorização.
- Não introduzir funcionalidades fora do escopo aprovado.
- Não inventar limites financeiros ou operacionais.
- Não modificar lançamentos originais do Ledger para corrigir operações.
- Não apagar histórico financeiro, auditoria ou tentativas da Outbox.
- Não iniciar implementação funcional durante a consolidação documental.
- Não considerar uma documentação validada sem verificar seu conteúdo efetivo.
- Não considerar a OpenAPI validada antes da geração e validação do arquivo.

## 9. Critério de conclusão

A consolidação documental será considerada concluída somente quando:

- Os documentos estiverem atualizados.
- As decisões estiverem consistentes entre si.
- As divergências identificadas estiverem resolvidas ou formalmente registradas.
- A especificação OpenAPI estiver materializada e validada.
- A auditoria cruzada estiver documentada.
- O usuário tiver revisado e aprovado o resultado.

**Fim do documento.**
