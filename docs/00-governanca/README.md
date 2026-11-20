# Biblioteca documental oficial do MyBank

| Campo | Valor |
| --- | --- |
| Identificador | MB-DOC-INDEX |
| Estado | Biblioteca documental oficial; revisão integral da Etapa 9 ainda aguarda aprovação humana. |

## Sumário

- [Princípios editoriais](#principios-editoriais)
- [Convenção de apresentação](#convencao-de-apresentacao)
- [Documentos oficiais](#documentos-oficiais)
- [Fontes preservadas](#fontes-preservadas)
- [Pendências e gate de aprovação](#pendencias-e-gate-de-aprovacao)

## Princípios editoriais

- Os diretórios numerados e os arquivos listados abaixo formam a biblioteca oficial de referência.
- `99-fontes-originais/` contém snapshots preservados antes da padronização. É arquivo histórico, não biblioteca normativa.
- A presença de um arquivo na biblioteca oficial não converte seu estado editorial interno de proposta em aprovação.
- Quando houver diferença, prevalecem decisões formalmente aprovadas e o registro explícito de pendências. A documentação lógica do DER e do dicionário v1.1 está aprovada; o original v1.0 permanece preservado como fonte histórica, e as pendências físicas ficam registradas para etapas futuras.
- O YAML é a fonte normativa única para o contrato OpenAPI; o Markdown correspondente é apenas uma página de orientação.
- Cada assunto possui uma fonte normativa definida nesta tabela. Especificações duplicadas foram substituídas por referências (em especial, o OpenAPI em YAML é a única fonte contratual); cópias históricas permanecem apenas no arquivo original, fora da biblioteca normativa.

## Convenção de apresentação

Caixas HTML devem ser usadas pontualmente para sinalizar informação relevante. Os nomes de classe têm significado semântico; não substituem o texto nem os títulos Markdown.

| Classe | Uso |
|---|---|
| `info-box` | Contexto ou informação geral. |
| `success-box` | Decisão ou critério aprovado/concluído. |
| `warning-box` | Pendência ou cautela que não bloqueia necessariamente a leitura. |
| `danger-box` | Restrição crítica de segurança ou integridade. |
| `note-box` | Observação complementar. |
| `example-box` | Exemplo ilustrativo, sem força normativa própria. |
| `definition-box` | Definição de um conceito ou termo. |

Não há renderizador local oficial nem mecanismo compartilhado de carregamento de CSS definido no projeto. O GitHub pode sanitizar atributos e não carrega CSS arbitrário do repositório; por isso, as caixas contêm rótulos e texto claros sem depender de cores. A apresentação Markdown convencional permanece legível. Não se declara compatibilidade visual universal.

## Documentos oficiais

| ID | Caminho oficial | Escopo | Estado do conteúdo |
|---|---|---|---|
| MB-GOV-AGENTS | [`../../AGENTS.md`](../../AGENTS.md) | Regras permanentes de trabalho e governança | Documento oficial de governança do projeto. |
| MB-GOV-ROADMAP | [`../../ROADMAP.md`](../../ROADMAP.md) | Fases, etapas, dependências e critérios de aceite | Documento oficial de planejamento; cada etapa mantém seu estado formal próprio. |
| MB-ADR | [`01-decisoes/adr.md`](../01-decisoes/adr.md) | Decisões arquiteturais | Catálogo com status geral proposto; ADRs individuais indicam estados próprios. |
| MB-DEC | [`01-decisoes/decisoes-tecnicas-complementares.md`](../01-decisoes/decisoes-tecnicas-complementares.md) | Decisões técnicas complementares | Decisões aprovadas, v1.1. |
| MB-REQ | [`02-requisitos/especificacao-funcional.md`](../02-requisitos/especificacao-funcional.md) | Requisitos funcionais e não funcionais | Proposta para aprovação. |
| MB-ARCH | [`03-arquitetura/arquitetura-e-diagramas.md`](../03-arquitetura/arquitetura-e-diagramas.md) | Arquitetura, módulos e diagramas | Proposta para aprovação. |
| MB-DATA | [`04-dados/der-e-dicionario-de-dados.md`](../04-dados/der-e-dicionario-de-dados.md) | DER e dicionário de dados | Documentação lógica v1.1 aprovada. A lista lógica mínima de tentativas e o método de reconciliação foram definidos; nomes/tipos/constraints/índices e a frequência concreta permanecem pendentes. |
| MB-DB-REV | [`04-dados/revisao-modelagem-e-pendencias.md`](../04-dados/revisao-modelagem-e-pendencias.md) | Revisão complementar de dados | Proposta de revisão, não substitui o DER/dicionário. |
| MB-API-REST | [`05-api/contratos-rest.md`](../05-api/contratos-rest.md) | Contratos REST | Proposta consolidada para revisão. |
| MB-API-OAS | [`05-api/openapi.yaml`](../05-api/openapi.yaml) | Contrato OpenAPI | OpenAPI 3.1.1; validação atual Redocly CLI 2.73.0: 0 erros, 3 avisos e 0 ignorados. Avisos de licença e servidores localhost permanecem. |
| MB-API-GUIDE | [`05-api/openapi.md`](../05-api/openapi.md) | Orientação da especificação OpenAPI | Índice para a única fonte YAML. |
| MB-FLOW | [`06-fluxos/fluxos-de-negocio.md`](../06-fluxos/fluxos-de-negocio.md) | Fluxos de negócio | Proposta para revisão. |
| MB-FE | [`07-frontend/especificacao-frontend.md`](../07-frontend/especificacao-frontend.md) | Especificação do frontend | Referência para desenvolvimento; decisões preservadas. |
| MB-OPS | [`08-operacao/parametros-operacionais.md`](../08-operacao/parametros-operacionais.md) | Parâmetros e regras operacionais | Proposta para aprovação. |
| MB-AUDIT | [`09-auditoria/relatorio-de-consolidacao.md`](../09-auditoria/relatorio-de-consolidacao.md) | Inventário, auditoria e validações | Relatório de trabalho; consolidação aguardando aprovação. |

## Fontes preservadas

Os documentos originais foram movidos, sem alteração de conteúdo, para [`99-fontes-originais/`](../99-fontes-originais/). O documento transversal “Consolidação da Documentação Técnica e Arquitetural”, ainda em estado de proposta, permanece somente como fonte histórica e não é documento normativo.

## Pendências e gate de aprovação

- Manter para etapas futuras os detalhes físicos de `outbox_event_attempts` e o valor concreto da frequência configurável de reconciliação de `attempt_count`; essas pendências não reabrem a aprovação lógica v1.1.
- A validação atual Redocly retornou 0 erros, 3 avisos e 0 ignorados; licença e endereços oficiais de produção não foram definidos. O resultado histórico de 50 avisos foi superado e está identificado como histórico no relatório.
- Revisar o relatório completo e aprovar a biblioteca documental.
- A Etapa 9 permanece aguardando aprovação; a Etapa 10 e a implementação funcional não estão autorizadas por esta organização documental.
