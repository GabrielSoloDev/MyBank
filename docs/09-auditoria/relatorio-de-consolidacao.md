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

O inventário inicial encontrou 12 documentos Markdown e uma especificação OpenAPI YAML. O DER aprovado e o dicionário de dados aprovados, referenciados como versão 1.0, não estavam presentes no repositório.

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
- OpenAPI: YAML 3.1.1 já havia passado pela validação sintática com PyYAML e por verificações direcionadas. A validação estrutural integral por ferramenta OpenAPI compatível continua pendente; nenhuma ferramenta está instalada e não houve instalação.

## 8. Pendências e aprovação

- Receber os documentos originais aprovados do DER e do dicionário de dados. Não foram reconstruídos campos, tipos ou relações ausentes.
- Executar validação estrutural completa da OpenAPI quando houver ferramenta compatível disponível ou autorização para instalá-la.
- Concluir a verificação automatizada de links e âncoras após a organização.
- Manter a Etapa 9 aguardando aprovação humana. A Etapa 10 e a implementação funcional não foram iniciadas.
