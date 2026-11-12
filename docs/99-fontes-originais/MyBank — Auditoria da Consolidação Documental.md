# MyBank — Auditoria da Consolidação Documental

**Data:** 2026-11-12 — revisão após solicitação AJUSTAR
**Escopo:** consolidação documental e auditoria cruzada; nenhuma implementação funcional.

## 1. Inventário

| Documento / artefato | Estado identificado | Observação |
|---|---|---|
| `AGENTS.md` | Regras permanentes | Atualizado com o estado oficial `FAILED` e o significado operacional de dead-letter. Agora aparece como arquivo não rastreado no Git. |
| `ROADMAP.md` | Planejamento com etapas em estados distintos | Etapa 9 permanece aguardando aprovação; Etapa 10 continua não iniciada. Agora aparece como arquivo não rastreado no Git. |
| `docs/MyBank — ADRs.md` | Catálogo com status geral “Proposto para consolidação documental”; vários ADRs internos `ACEITO` | Recebeu nota de rastreabilidade sem mudar o status geral. |
| `docs/MyBank — Especificação Funcional Consolidada.md` | Versão 1.0, proposta para aprovação | Recebeu complemento das decisões aprovadas v1.1. |
| `docs/MyBank — Arquitetura e Diagramas.md` | Versão 1.0, proposta para aprovação | Recebeu complemento de segurança do CPF e responsabilidades da Outbox. |
| `docs/MyBank — Revisão da Modelagem do Banco de Dados.md` | Versão 1.1, proposta de revisão | Complementa um DER/dicionário v1.0 que não está no repositório. |
| `docs/MyBank — Especificação Consolidada dos Contratos da API REST.md` | Versão 1.1, proposta consolidada para revisão | Recebeu o endpoint administrativo, resposta assíncrona e consulta de status. |
| `docs/MyBank — Especificação OpenAPI 3.1.1.md` | API 1.1, OpenAPI 3.1.1, proposta para revisão | Continha um bloco YAML, sem artefato `.yaml` materializado. O bloco foi preservado e sincronizado. |
| `docs/04-api/openapi.yaml` | Criado nesta etapa a partir do bloco YAML existente | Artefato materializado; não reconstruído do zero. |
| `docs/MyBank — Especificação Frontend.md` | Versão 1.0, referência para desenvolvimento | Complementada com comportamento do frontend da Outbox e privacidade do CPF. |
| `docs/MyBank — Fluxos Detalhados de Negocio.md` | Versão 1.0, proposta para revisão | Recebeu fluxo administrativo de reprocessamento. |
| `docs/MyBank — Consolidação das Decisões Técnicas Complementares.md` | Versão 1.1, decisões aprovadas | Mantida como fonte aprovada, sem alteração. |
| `docs/MyBank — Definição dos Parâmetros Operacionais.md` | Versão 1.0, proposta para aprovação | Mantida; limites numéricos não foram inventados. |
| `docs/MyBank — Consolidação da Documentação Técnica e Arquitetural.md` | Versão 1.0, aguardando aprovação | Mantida; proposta anterior não foi tratada como aprovada. |

O inventário inicial encontrou 11 documentos Markdown em `docs/`, além de `AGENTS.md` e `ROADMAP.md`; este relatório de auditoria eleva o total atual para 12. Não foram encontradas cópias duplicadas dos documentos na raiz. O DER e o dicionário de dados citados como versão 1.0 estão ausentes. O OpenAPI Markdown e o novo YAML contêm representações sincronizadas da especificação e exigem manutenção conjunta enquanto ambas permanecerem publicadas.

O `git status` estava limpo antes do trabalho. `AGENTS.md` e `ROADMAP.md` são explicitamente ignorados pelo `.gitignore`; suas edições são locais e não aparecem como mudanças versionáveis sem alteração dessa regra, que ficou fora do escopo.

## 2. Decisões incorporadas

- CPF como único documento aceito na primeira versão, obrigatório, validado e único.
- Proteção do CPF com AES-256-GCM e HMAC-SHA-256 independente; chaves fora do banco e do código-fonte, com versionamento e rotação.
- Snapshot contratual de empréstimo imutável e preservado após quitação.
- Estornos administrativos integrais, com autorização ADMIN, 2FA, reautenticação, justificativa, confirmação, auditoria, idempotência, limites configuráveis e lançamentos compensatórios.
- Reprocessamento administrativo idempotente, auditado e concorrencialmente controlado, preservando tentativas e payload original.
- Endpoint `POST /api/v1/admin/outbox-events/{eventId}/reprocess`, `202 Accepted` e `statusUrl`.
- Endpoint de consulta GET para o caminho indicado por `statusUrl`.

Nenhum valor numérico de limite de estorno ou parâmetro operacional foi acrescentado.

## 3. Divergências identificadas

### Registradas sem decisão unilateral

1. **Persistência de CPF e snapshot:** a decisão aprovada define dados a preservar, mas não há DER/dicionário v1.0 para revisar campos, tipos, índices e restrições sem reconstrução especulativa. Os documentos originais foram solicitados para concluir essa auditoria.

### Corrigidas nesta revisão

- `FAILED` foi adotado como estado terminal oficial e elegível a reprocessamento. `DEAD_LETTER` é apenas encaminhamento/armazenamento operacional, não estado alternativo.
- O endpoint e os contratos aceitam reprocessamento somente de eventos elegíveis em `FAILED`.
- O exemplo da consolidação complementar v1.1 agora inclui `confirmation: true`; contratos e OpenAPI mantêm confirmação, justificativa, reautenticação e auditoria.
- A ausência do arquivo OpenAPI standalone foi suprida por extração do bloco YAML existente, preservando a especificação e adicionando os contratos aprovados.
- `statusUrl` aponta para um recurso GET descrito na OpenAPI; a resposta `202` contém `eventId`, `status`, `message` e `statusUrl`, com estado inicial `PENDING`.
- As regras de `.gitignore` foram ajustadas para permitir versionar `AGENTS.md` e `ROADMAP.md`, preservando seu conteúdo.

## 4. Resultado da validação OpenAPI

- **Versão declarada:** 3.1.1.
- **Sintaxe YAML:** aprovada pelo parser PyYAML 6.0.3 instalado no ambiente.
- **Checagens estruturais direcionadas:** verificadas versão, presença das duas rotas da Outbox, resposta 202 referenciando o schema dedicado, segurança bearer global e parâmetros de evento, idempotência e reautenticação.
- **Validação integral por ferramenta OpenAPI compatível:** não executada. `redocly`, `swagger-cli`, `openapi-generator-cli`, `openapi_spec_validator` e `openapi_schema_validator` não estão disponíveis. A tentativa anterior `npx --offline --no-install @redocly/cli lint docs/04-api/openapi.yaml` terminou com `ENOTCACHED`. A instalação de ferramentas está proibida nesta etapa; portanto, a especificação não é declarada estruturalmente validada.

## 5. Auditoria cruzada final

| Tema | Resultado |
|---|---|
| CPF nos requisitos e proteção criptográfica | Registrado nos requisitos e arquitetura. A representação DER/dicionário permanece pendente pela ausência dos documentos-base. |
| Chaves fora do banco e do código-fonte | Reafirmado nos requisitos permanentes e arquitetura. |
| Snapshot imutável do empréstimo | Reafirmado nos requisitos e documentação funcional; materialização dos campos no DER depende do modelo-base ausente. |
| Integridade e imutabilidade do Ledger | Mantidas pelas regras existentes; estorno descrito por lançamentos compensatórios. |
| Regras de estorno | Integral, controlado e condicionado a limites configurados; sem valores inventados. |
| Segurança administrativa | ADMIN, 2FA, reautenticação, justificativa, confirmação e auditoria refletidos nos contratos. |
| Idempotência e concorrência da Outbox | Refletidas no contrato e na OpenAPI. |
| Preservação do histórico de tentativas | Refletida em requisitos, fluxo, frontend e contratos; permanece sem alteração destrutiva. |
| Contrato assíncrono e `statusUrl` | `202 Accepted` e recurso GET de consulta incluídos na OpenAPI e contratos REST. |
| Correspondência frontend/API | Frontend orientado a acompanhar a `statusUrl` e preservar tentativas; elegibilidade permanece sob autoridade do backend. |
| Máquina de estados | `FAILED` é o estado terminal oficial; `DEAD_LETTER` é encaminhamento/armazenamento operacional. Reprocessamento exige estado `FAILED` e elegibilidade. |

## 6. Critérios de aceite

- Inventário e estado local verificados antes das edições: **atendido**.
- Decisões aprovadas incorporadas aos documentos disponíveis: **parcialmente atendido**, pois DER e dicionário-base estão ausentes.
- OpenAPI 3.1.1 localizada e endpoint integrado: **atendido**.
- YAML sintaticamente validado: **atendido**.
- OpenAPI validada integralmente por ferramenta compatível: **não atendido**; ferramenta não instalada e instalação vedada.
- Auditoria cruzada concluída com pendências explicitadas: **atendido**.
- Implementação funcional ou avanço ao ambiente: **não iniciado**, conforme restrição.

## 7. Git e próxima etapa recomendada

`git check-ignore -v AGENTS.md ROADMAP.md` identificou as regras originais nas linhas 1 e 2 de `.gitignore`. As exceções `!AGENTS.md` e `!ROADMAP.md` foram adicionadas sem apagar ou substituir as regras anteriores. Após a alteração, `git check-ignore -v` mostra as exceções e `git status --short` lista ambos os arquivos como não rastreados (`??`), prontos para versionamento.

Disponibilizar os documentos originais aprovados do DER e do dicionário de dados para concluir a auditoria de CPF e snapshot. Executar validação integral da OpenAPI em ambiente que já possua ferramenta compatível. A máquina de estados da Outbox e o corpo com `confirmation: true` foram harmonizados documentalmente. A Etapa 9 permanece aguardando aprovação; a Etapa 10 não foi iniciada.
