# Modelo de dados — DER e Dicionário de Dados

| Campo | Valor |
|---|---|
| Identificador | MB-DATA-INDEX |
| Estado editorial | Documentação lógica v1.1 aprovada; pendências físicas registradas para etapas futuras. |

## Documentos

- [DER e Dicionário de Dados v1.1](der-e-dicionario-de-dados.md): modelo lógico aprovado; a estrutura física de `outbox_event_attempts` e os detalhes operacionais da reconciliação permanecem pendentes para etapas futuras.
- [Revisão da modelagem e pendências](revisao-modelagem-e-pendencias.md): complemento proposto; somente regras posteriormente confirmadas são tratadas como aprovadas.
- [Documento original de modelagem v1.0](../99-fontes-originais/MyBank%20—%20Documento%20de%20Modelagem%20do%20Banco%20de%20Dados.md): preservado intacto e identificado como proposta para revisão.

## Situação

O DER e o Dicionário de Dados v1.1 incorporam 38 entidades-base e quatro estruturas lógicas aprovadas. A documentação lógica está aprovada. A estrutura física de `outbox_event_attempts` e a forma/frequência operacional da reconciliação de `attempt_count` permanecem pendentes e não foram definidas nesta etapa.

A aprovação da documentação lógica de dados não encerra a Etapa 9, não aprova integralmente a biblioteca documental e não autoriza SQL, migrations, implementação física ou desenvolvimento funcional.
