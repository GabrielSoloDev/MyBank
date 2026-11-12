# Biblioteca documental oficial do MyBank

| Campo | Valor |
| --- | --- |
| Identificador | MB-DOC-INDEX |
| Estado | Estrutura documental oficial; consolidação aguardando aprovação humana. |

## Sumário

- [Princípios editoriais](#principios-editoriais)
- [Documentos oficiais](#documentos-oficiais)
- [Fontes preservadas](#fontes-preservadas)
- [Pendências e gate de aprovação](#pendencias-e-gate-de-aprovacao)

## Princípios editoriais

- Os diretórios numerados e os arquivos listados abaixo formam a biblioteca oficial de referência.
- `99-fontes-originais/` contém snapshots preservados antes da padronização. É arquivo histórico, não biblioteca normativa.
- A presença de um arquivo na biblioteca oficial não converte seu estado editorial interno de proposta em aprovação.
- Quando houver diferença, prevalecem decisões formalmente aprovadas e o registro explícito de pendências. O DER e o dicionário de dados originais continuam necessários.
- O YAML é a fonte normativa única para o contrato OpenAPI; o Markdown correspondente é apenas uma página de orientação.
- Cada assunto possui uma fonte normativa definida nesta tabela. Especificações duplicadas foram substituídas por referências (em especial, o OpenAPI em YAML é a única fonte contratual); cópias históricas permanecem apenas no arquivo original, fora da biblioteca normativa.

## Documentos oficiais

| ID | Caminho oficial | Escopo | Estado do conteúdo |
|---|---|---|---|
| MB-ADR | [`01-decisoes/adr.md`](../01-decisoes/adr.md) | Decisões arquiteturais | Catálogo com status geral proposto; ADRs individuais indicam estados próprios. |
| MB-DEC | [`01-decisoes/decisoes-tecnicas-complementares.md`](../01-decisoes/decisoes-tecnicas-complementares.md) | Decisões técnicas complementares | Decisões aprovadas, v1.1. |
| MB-REQ | [`02-requisitos/especificacao-funcional.md`](../02-requisitos/especificacao-funcional.md) | Requisitos funcionais e não funcionais | Proposta para aprovação. |
| MB-ARCH | [`03-arquitetura/arquitetura-e-diagramas.md`](../03-arquitetura/arquitetura-e-diagramas.md) | Arquitetura, módulos e diagramas | Proposta para aprovação. |
| MB-DATA | [`04-dados/README.md`](../04-dados/README.md) | Modelo de dados e pendências | DER e dicionário ausentes; não consolidado. |
| MB-DB-REV | [`04-dados/revisao-modelagem-e-pendencias.md`](../04-dados/revisao-modelagem-e-pendencias.md) | Revisão complementar de dados | Proposta de revisão, não substitui o DER/dicionário. |
| MB-API-REST | [`05-api/contratos-rest.md`](../05-api/contratos-rest.md) | Contratos REST | Proposta consolidada para revisão. |
| MB-API-OAS | [`05-api/openapi.yaml`](../05-api/openapi.yaml) | Contrato OpenAPI | OpenAPI 3.1.1; validação estrutural concluída com Redocly CLI 2.73.0: 0 erros, 50 avisos e 0 ignorados. Avisos de qualidade e pendências semânticas/documentais permanecem em análise. |
| MB-API-GUIDE | [`05-api/openapi.md`](../05-api/openapi.md) | Orientação da especificação OpenAPI | Índice para a única fonte YAML. |
| MB-FLOW | [`06-fluxos/fluxos-de-negocio.md`](../06-fluxos/fluxos-de-negocio.md) | Fluxos de negócio | Proposta para revisão. |
| MB-FE | [`07-frontend/especificacao-frontend.md`](../07-frontend/especificacao-frontend.md) | Especificação do frontend | Referência para desenvolvimento; decisões preservadas. |
| MB-OPS | [`08-operacao/parametros-operacionais.md`](../08-operacao/parametros-operacionais.md) | Parâmetros e regras operacionais | Proposta para aprovação. |
| MB-AUDIT | [`09-auditoria/relatorio-de-consolidacao.md`](../09-auditoria/relatorio-de-consolidacao.md) | Inventário, auditoria e validações | Relatório de trabalho; consolidação aguardando aprovação. |

## Fontes preservadas

Os documentos originais foram movidos, sem alteração de conteúdo, para [`99-fontes-originais/`](../99-fontes-originais/). O documento transversal “Consolidação da Documentação Técnica e Arquitetural”, ainda em estado de proposta, permanece somente como fonte histórica e não é documento normativo.

## Pendências e gate de aprovação

- Receber e auditar o DER e o dicionário de dados originais aprovados.
- Revisar os 50 avisos Redocly e as pendências semânticas/documentais registradas no relatório, sem considerar a validação estrutural como aprovação integral do contrato.
- Revisar o relatório completo e aprovar a biblioteca documental.
- A Etapa 9 permanece aguardando aprovação; a Etapa 10 e a implementação funcional não estão autorizadas por esta organização documental.
