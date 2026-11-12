# MyBank — Auditoria da Consolidação Documental

**Data:** 2026-11-12  
**Escopo:** consolidação documental e auditoria cruzada; nenhuma implementação funcional.

## 1. Inventário

| Documento / artefato | Estado identificado | Observação |
|---|---|---|
| `AGENTS.md` | Regras permanentes; arquivo local ignorado pelo Git | Atualizado para registrar decisões complementares e a pendência de estado da Outbox. |
| `ROADMAP.md` | Planejamento com etapas em estados distintos; arquivo local ignorado pelo Git | Etapa 9 marcada como aguardando aprovação; Etapa 10 continua não iniciada. |
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

1. **Estado terminal da Outbox:** a consolidação aprovada v1.1 considera `FAILED` terminal e elegível; a revisão da modelagem declara que `FAILED` não é terminal, usa `DEAD_LETTER` após esgotar tentativas e permite reprocessar esse estado. Requer decisão explícita para harmonizar estados, elegibilidade e persistência.
2. **Corpo do reprocessamento:** a consolidação v1.1 lista confirmação adicional como requisito, mas o exemplo JSON contém apenas `reason`. Os contratos atualizados e OpenAPI exigem `confirmation: true`, seguindo o requisito de confirmação; o exemplo da fonte aprovada permanece divergente e precisa de ratificação editorial.
3. **Persistência de CPF e snapshot:** a decisão aprovada define dados a preservar, mas não há DER/dicionário v1.0 para receber campos, tipos, índices e restrições sem reconstrução especulativa.

### Resolvidas dentro do escopo documental

- A ausência do arquivo OpenAPI standalone foi suprida por extração do bloco YAML já existente, preservando a especificação completa e adicionando os contratos aprovados.
- `statusUrl` agora aponta para um recurso GET descrito na OpenAPI.
- A resposta `202` usa os campos aprovados (`eventId`, `status`, `message`, `statusUrl`) e o estado inicial `PENDING`.

## 4. Resultado da validação OpenAPI

- **Versão declarada:** 3.1.1.
- **Sintaxe YAML:** aprovada pelo parser PyYAML 6.0.3 instalado no ambiente.
- **Checagens estruturais direcionadas:** verificadas versão, presença das duas rotas da Outbox, resposta 202 referenciando o schema dedicado, segurança bearer global e parâmetros de evento, idempotência e reautenticação.
- **Validação integral por ferramenta OpenAPI compatível:** não executada. `@redocly/cli` não está disponível localmente e a tentativa offline com `npx --no-install` terminou com `ENOTCACHED`. A instalação de ferramentas está proibida nesta etapa; portanto, a especificação não é declarada estruturalmente validada.

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
| Máquina de estados | Inconsistente entre `FAILED` e `DEAD_LETTER`; não resolvida. |

## 6. Critérios de aceite

- Inventário e estado local verificados antes das edições: **atendido**.
- Decisões aprovadas incorporadas aos documentos disponíveis: **parcialmente atendido**, pois DER e dicionário-base estão ausentes e a divergência da Outbox permanece aberta.
- OpenAPI 3.1.1 localizada e endpoint integrado: **atendido**.
- YAML sintaticamente validado: **atendido**.
- OpenAPI validada integralmente por ferramenta compatível: **não atendido**; ferramenta não instalada e instalação vedada.
- Auditoria cruzada concluída com pendências explicitadas: **atendido**.
- Implementação funcional ou avanço ao ambiente: **não iniciado**, conforme restrição.

## 7. Próxima etapa recomendada

Revisar as pendências documentais, decidir a máquina de estados terminal da Outbox e confirmar o requisito de `confirmation` no corpo. Disponibilizar o DER/dicionário aprovado e executar validação integral da OpenAPI em ambiente que já possua ferramenta compatível. A Etapa 9 permanece aguardando aprovação; a Etapa 10 não foi iniciada.
