# Especificação OpenAPI da API MyBank

| Campo | Valor |
| --- | --- |
| Identificador | MB-API-OPENAPI |
| Estado editorial | Oficial como índice da especificação; conteúdo contratual no YAML. |
| Versão OpenAPI | 3.1.1 |
| Versão da API | 1.1.0 |
| Fonte normativa | [openapi.yaml](openapi.yaml) |
| Fonte Markdown anterior | [original arquivado](../99-fontes-originais/MyBank%20%E2%80%94%20Especifica%C3%A7%C3%A3o%20OpenAPI%203.1.1.md) |

## Sumário

- [Fonte normativa](#fonte-normativa)
- [Histórico e preservação](#historico-e-preservacao)
- [Validação](#validacao)

## Fonte normativa

<aside class="note-box" role="note" aria-label="Fonte do contrato">
<strong>Fonte do contrato.</strong>
<p>O YAML é a única representação normativa da API. Esta página é um índice; alterações contratuais devem ser feitas e validadas em <code>openapi.yaml</code>.</p>
</aside>

O contrato executável da especificação é mantido exclusivamente em [`openapi.yaml`](openapi.yaml), versão OpenAPI 3.1.1. Ele contém endpoints, parâmetros, schemas, respostas, segurança e exemplos.

## Histórico e preservação

A especificação Markdown anterior, incluindo seu bloco YAML incorporado, foi preservada sem alterações em `docs/99-fontes-originais/`. Para evitar duas fontes normativas divergentes, esta página aponta para o YAML oficial.

## Validação

A validação executada em 2026-11-20 com Redocly CLI 2.73.0 retornou 0 erros, 3 avisos e 0 ignorados. Os avisos são a falta de licença formal e os dois endereços localhost, mantidos sem inventar URL de produção. O comando e as localizações estão no [relatório de consolidação](../09-auditoria/relatorio-de-consolidacao.md). Esta validação estrutural não aprova a Etapa 9 integral.
