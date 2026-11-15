# Relatório da consolidação documental

| Campo | Valor |
| --- | --- |
| Identificador | MB-AUDIT-001 |
| Classe | Documento oficial da biblioteca MyBank |
| Estado | Consolidação documental organizada; aguardando aprovação humana |
| Data | 2026-11-12 |

## Sumário

- [1. Objetivo e escopo](#1-objetivo-e-escopo)
- [2. Inventário](#2-inventario)
- [3. Documentos oficiais](#3-documentos-oficiais)
- [4. Fontes preservadas e movimentadas](#4-fontes-preservadas-e-movimentadas)
- [5. Padrões aplicados](#5-padroes-aplicados)
- [6. Duplicidades e referências](#6-duplicidades-e-referencias)
- [7. Validações](#7-validacoes)
- [8. Pendências e aprovação](#8-pendencias-e-aprovacao)

## 1. Objetivo e escopo

Organizar e padronizar a documentação existente em diretórios oficiais numerados, atualizar referências internas e preservar os documentos de origem até a aprovação humana. Nenhuma implementação funcional ou preparação de ambiente foi iniciada.

## 2. Inventário

O inventário inicial desta consolidação encontrou 12 documentos Markdown e uma especificação OpenAPI YAML. Naquele momento, os documentos de modelagem então procurados não estavam presentes no repositório; esse registro é histórico e foi superado pela elaboração e aprovação posterior da documentação lógica DER e Dicionário de Dados v1.1. O documento original v1.0 permanece preservado como fonte histórica, sem substituir a versão lógica aprovada.

O documento transversal **Consolidação da Documentação Técnica e Arquitetural** estava em proposta para revisão. Por preservar seu estado, ele permanece somente como fonte arquivada e não foi declarado oficial.

## 3. Documentos oficiais

A lista normativa, o identificador, o escopo e o estado editorial de cada documento constam no [índice oficial](../00-governanca/README.md). O conteúdo pode continuar com status de proposta ou revisão; a classificação como arquivo oficial não altera esse status.

A estrutura oficial organiza governança, decisões, requisitos, arquitetura, dados, API, fluxos, frontend, operação e auditoria nos diretórios `00` a `09`. O OpenAPI YAML em [`../05-api/openapi.yaml`](../05-api/openapi.yaml) é a fonte contratual única.

## 4. Fontes preservadas e movimentadas

Os documentos Markdown e o YAML listados abaixo foram movidos, sem transformação de conteúdo, para `docs/99-fontes-originais/`; a pasta inclui um README orientando o uso do arquivo histórico. As cópias arquivadas são fontes históricas não normativas; as versões oficiais estão nos caminhos indicados.

| Fonte original arquivada | Documento oficial |
|---|---|
| `MyBank — ADRs.md` | [`01-decisoes/adr.md`](../01-decisoes/adr.md) |
| `MyBank — Consolidação das Decisões Técnicas Complementares.md` | [`01-decisoes/decisoes-tecnicas-complementares.md`](../01-decisoes/decisoes-tecnicas-complementares.md) |
| `MyBank — Especificação Funcional Consolidada.md` | [`02-requisitos/especificacao-funcional.md`](../02-requisitos/especificacao-funcional.md) |
| `MyBank — Arquitetura e Diagramas.md` | [`03-arquitetura/arquitetura-e-diagramas.md`](../03-arquitetura/arquitetura-e-diagramas.md) |
| `MyBank — Revisão da Modelagem do Banco de Dados.md` | [`04-dados/revisao-modelagem-e-pendencias.md`](../04-dados/revisao-modelagem-e-pendencias.md) |
| `MyBank — Especificação Consolidada dos Contratos da API REST.md` | [`05-api/contratos-rest.md`](../05-api/contratos-rest.md) |
| `MyBank — Especificação OpenAPI 3.1.1.md` | [`05-api/openapi.md`](../05-api/openapi.md), com contrato em [`05-api/openapi.yaml`](../05-api/openapi.yaml) |
| `MyBank — Fluxos Detalhados de Negocio.md` | [`06-fluxos/fluxos-de-negocio.md`](../06-fluxos/fluxos-de-negocio.md) |
| `MyBank — Especificação Frontend.md` | [`07-frontend/especificacao-frontend.md`](../07-frontend/especificacao-frontend.md) |
| `MyBank — Definição dos Parâmetros Operacionais.md` | [`08-operacao/parametros-operacionais.md`](../08-operacao/parametros-operacionais.md) |
| `MyBank — Auditoria da Consolidação Documental.md` | Este relatório, atualizado para a estrutura atual |
| `MyBank — Consolidação da Documentação Técnica e Arquitetural.md` | Somente fonte histórica, não oficial |
| `04-api/openapi.yaml` | [`05-api/openapi.yaml`](../05-api/openapi.yaml) |

`AGENTS.md` e `ROADMAP.md` permanecem na raiz e agora aparecem no Git após a alteração das regras de ignore. O conteúdo original deles foi mantido, com atualização das referências documentais e sumários.

## 5. Padrões aplicados

- Diretórios numerados e identificadores editoriais estáveis.
- Nomes oficiais em minúsculas, ASCII e kebab-case.
- Título documental único em H1; hierarquia de seções preservada e harmonizada abaixo dele.
- Sumário com links para as seções principais.
- Espaçamento consistente em títulos, tabelas e blocos de código.
- Títulos e identificadores de negócio existentes, como ADR e RF, foram preservados; a numeração duplicada do apêndice de dados foi corrigida.
- Diagramas Mermaid passaram a usar cercas Markdown normais; wrappers de escape foram removidos das versões oficiais.
- Documentos históricos mantidos sem transformação em `99-fontes-originais/`.

## 6. Duplicidades e referências

O OpenAPI deixou de ter duas cópias normativas: o documento Markdown extenso foi arquivado e substituído por uma página de orientação que aponta ao YAML oficial. O índice tabular duplicado dos ADRs foi substituído por sumário navegável. O documento transversal ainda em proposta foi mantido apenas no arquivo histórico.

A biblioteca oficial usa um único índice para declarar fonte normativa por assunto. Repetições necessárias para rastreabilidade entre requisitos, arquitetura, fluxo e contratos foram mantidas como referências ao documento autoritativo correspondente. Os links internos da biblioteca oficial e as referências de `AGENTS.md` e `ROADMAP.md` foram atualizados.

## 7. Validações

- `git diff --check`: aprovado.
- Integridade de cópias arquivadas: arquivos foram movidos sem transformação; as versões oficiais foram geradas a partir desses conteúdos.
- Markdown (arquivos oficiais, governança raiz e README do arquivo): 16 arquivos, 653 títulos, 74 tabelas; links, âncoras, hierarquia, cercas e espaçamento aprovados sem erros. Os snapshots históricos permanecem inalterados e foram excluídos da normalização.
- Diagramas e blocos de código: cercas revisadas quanto a balanceamento; tags de escape removidas das versões oficiais.
- OpenAPI 3.1.1: validada com Redocly CLI `2.73.0`, versão executada por `npx` com cache temporário, sem instalação global. Comando: `npm_config_cache=/tmp/mybank-redocly-cache npm_config_offline=true npx --yes @redocly/cli@2.73.0 lint --format=json docs/05-api/openapi.yaml`. Resultado: 0 erros, 50 avisos e 0 ignorados; saída final: `Your API description is valid.`
- Na auditoria documental mais recente, não foi possível repetir a validação Redocly porque a ferramenta não estava disponível no ambiente. Nenhuma ferramenta foi instalada. O resultado acima permanece o registro de uma execução histórica e não comprova uma validação integral atual.
- Auditoria estrutural adicional: 106 path items e 116 operações; `operationId` únicos; parâmetros de rota obrigatórios presentes; respostas definidas; 585 referências locais resolvidas; nenhuma referência externa ou ciclo de schema detectado.
- Avisos Redocly registrados sem supressão:
  - `info-license-strict`: linha 23, `#/info/license`; licença sem URL ou identificador.
  - `no-server-example.com`: linha 27, `#/servers/0/url`, e linha 29, `#/servers/1/url`; URLs de servidor apontam para localhost.
  - `tag-description`: 14 ocorrências nas linhas 33–44 e 50–51, pointers `#/tags/0/description` até `#/tags/11/description`, além de `#/tags/13/description` e `#/tags/14/description`; tags sem descrição.
  - `no-illogical-composition-keywords`: linha 3112, `#/components/schemas/AuthenticationResponse/properties/data/allOf`; `allOf` contém um único schema.
  - `operation-4xx-response`: 30 operações não declaram resposta 4XX:
    - linha 521: `#/paths/~1customers~1me~1accounts/get/responses`
    - linha 534: `#/paths/~1customers~1me~1loans/get/responses`
    - linha 547: `#/paths/~1customers~1me~1cards/get/responses`
    - linha 560: `#/paths/~1customers~1me~1notifications/get/responses`
    - linha 581: `#/paths/~1accounts/get/responses`
    - linha 773: `#/paths/~1accounts~1{accountId}~1holders/get/responses`
    - linha 788: `#/paths/~1accounts~1{accountId}~1invitations/get/responses`
    - linha 951: `#/paths/~1accounts~1{accountId}~1transfers/get/responses`
    - linha 1008: `#/paths/~1accounts~1{accountId}~1deposits/get/responses`
    - linha 1065: `#/paths/~1accounts~1{accountId}~1withdrawals/get/responses`
    - linha 1099: `#/paths/~1payment-providers/get/responses`
    - linha 1181: `#/paths/~1accounts~1{accountId}~1payments/get/responses`
    - linha 1222: `#/paths/~1loans/get/responses`
    - linha 1275: `#/paths/~1loans~1{loanId}~1installments/get/responses`
    - linha 1309: `#/paths/~1loans~1{loanId}~1installments~1{installmentId}~1payments/get/responses`
    - linha 1392: `#/paths/~1cards/get/responses`
    - linha 1524: `#/paths/~1cards~1{cardId}~1transactions/get/responses`
    - linha 2002: `#/paths/~1admin~1accounts/get/responses`
    - linha 2049: `#/paths/~1admin~1accounts~1{accountId}~1block/post/responses`
    - linha 2075: `#/paths/~1admin~1accounts~1{accountId}~1unblock/post/responses`
    - linha 2101: `#/paths/~1admin~1accounts~1{accountId}~1suspend/post/responses`
    - linha 2146: `#/paths/~1admin~1administrators/get/responses`
    - linha 2163: `#/paths/~1admin~1administrators~1invitations/get/responses`
    - linha 2207: `#/paths/~1admin~1administrators~1invitations~1{invitationId}~1cancel/post/responses`
    - linha 2272: `#/paths/~1admin~1settings/get/responses`
    - linha 2339: `#/paths/~1notifications/get/responses`
    - linha 2382: `#/paths/~1notifications~1read-all/post/responses`
    - linha 2391: `#/paths/~1notifications~1preferences/get/responses`
    - linha 2408: `#/paths/~1notifications~1preferences/patch/responses`
    - linha 2500: `#/paths/~1privacy~1requests/get/responses`
  - `no-unused-components`: linha 2911, `#/components/schemas/ResponseMeta` (schema não utilizado); linha 2566, `#/components/securitySchemes/reauthenticationToken` (security scheme sem uso em requisitos `security`; reautenticação segue documentada por parâmetro de cabeçalho nas operações aplicáveis).
- Nenhum aviso foi automaticamente descartado.
- Não foi executada segunda validação estrutural independente; não havia outra ferramenta compatível disponível, e nenhuma foi instalada.
- Atualização posterior da revisão semântica: `GET /admin/loans` agora exige somente Bearer para autenticação, não exige reautenticação contextual e documenta auditoria; as 31 operações `/admin/**` têm requisitos explícitos de perfil ADMIN, 2FA e autorização do backend. Outbox documenta `PROCESSED`, `FAILED` e o significado operacional de `DEAD_LETTER`.

## 8. Pendências e aprovação

- A documentação lógica DER e Dicionário de Dados v1.1 foi aprovada pelo usuário. O documento original v1.0 permanece preservado; continuam pendentes para etapas futuras o detalhamento físico de `outbox_event_attempts` e os detalhes operacionais da reconciliação. Essas pendências não invalidam a aprovação lógica.
- Repetir a validação Redocly em etapa autorizada, pois a auditoria mais recente não conseguiu executá-la; o resultado registrado de 0 erros e 50 avisos é histórico.
- Analisar os 50 avisos do Redocly e decidir separadamente quais ajustes de qualidade devem ser aprovados.
- A estrutura detalhada dos objetos do histórico de tentativas da Outbox continua pendente; as fontes aprovadas não especificam os campos, e o schema permaneceu genérico.
- Concluir a verificação automatizada de links e âncoras após a organização.
- Manter a Etapa 9 aguardando aprovação humana. A Etapa 10 e a implementação funcional não foram iniciadas.
