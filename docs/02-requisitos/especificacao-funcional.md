# MyBank — Especificação Funcional Consolidada

| Campo | Valor |
| --- | --- |
| Identificador | MB-REQ-001 |
| Classe | Documento oficial da biblioteca MyBank |
| Original preservado | [MyBank — Especificação Funcional Consolidada.md](../99-fontes-originais/MyBank%20—%20Especificação%20Funcional%20Consolidada.md) |
| Versão | 1.0 — Proposta para aprovação |
| Projeto | MyBank |
| Slogan | Seu dinheiro. Suas possibilidades. |
| Natureza | Sistema bancário digital fictício para fins educacionais e de portfólio. |

## Sumário

- [1. Visão geral](#1-visao-geral)
- [1.1. Objetivo](#11-objetivo)
- [1.2. Perfis de usuário](#12-perfis-de-usuario)
- [2. Requisitos funcionais](#2-requisitos-funcionais)
- [RF-001 — Cadastro de clientes](#rf-001-cadastro-de-clientes)
- [RF-002 — Autenticação](#rf-002-autenticacao)
- [RF-003 — Autenticação em dois fatores](#rf-003-autenticacao-em-dois-fatores)
- [RF-004 — Gestão de clientes](#rf-004-gestao-de-clientes)
- [RF-005 — Gestão de contas bancárias](#rf-005-gestao-de-contas-bancarias)
- [RF-006 — Consulta de saldo](#rf-006-consulta-de-saldo)
- [RF-007 — Extrato financeiro](#rf-007-extrato-financeiro)
- [RF-008 — Transferências internas](#rf-008-transferencias-internas)
- [RF-009 — Depósitos simulados](#rf-009-depositos-simulados)
- [RF-010 — Saques simulados](#rf-010-saques-simulados)
- [RF-011 — Pagamentos](#rf-011-pagamentos)
- [RF-012 — Empréstimos](#rf-012-emprestimos)
- [RF-013 — Cartões virtuais](#rf-013-cartoes-virtuais)
- [RF-014 — Notificações](#rf-014-notificacoes)
- [RF-015 — Perfil do cliente](#rf-015-perfil-do-cliente)
- [RF-016 — Segurança do cliente](#rf-016-seguranca-do-cliente)
- [RF-017 — Dashboard do cliente](#rf-017-dashboard-do-cliente)
- [RF-018 — Administração do sistema](#rf-018-administracao-do-sistema)
- [RF-019 — Dashboard administrativo](#rf-019-dashboard-administrativo)
- [RF-020 — Auditoria](#rf-020-auditoria)
- [RF-021 — Privacidade e LGPD](#rf-021-privacidade-e-lgpd)
- [3. Requisitos não funcionais](#3-requisitos-nao-funcionais)
- [RNF-001 — Arquitetura](#rnf-001-arquitetura)
- [RNF-002 — Segurança](#rnf-002-seguranca)
- [RNF-003 — Consistência financeira](#rnf-003-consistencia-financeira)
- [RNF-004 — Precisão monetária](#rnf-004-precisao-monetaria)
- [RNF-005 — Desempenho](#rnf-005-desempenho)
- [RNF-006 — Disponibilidade e recuperação](#rnf-006-disponibilidade-e-recuperacao)
- [RNF-007 — Manutenibilidade](#rnf-007-manutenibilidade)
- [RNF-008 — Testabilidade](#rnf-008-testabilidade)
- [RNF-009 — Portabilidade e reprodução](#rnf-009-portabilidade-e-reproducao)
- [RNF-010 — Usabilidade](#rnf-010-usabilidade)
- [RNF-011 — Acessibilidade](#rnf-011-acessibilidade)
- [RNF-012 — Privacidade](#rnf-012-privacidade)
- [4. Matriz de permissões](#4-matriz-de-permissoes)
- [5. Máquinas de estados](#5-maquinas-de-estados)
- [5.1. Transações](#51-transacoes)
- [5.2. Pagamentos](#52-pagamentos)
- [5.3. Empréstimos](#53-emprestimos)
- [5.4. Convites de titulares](#54-convites-de-titulares)
- [5.5. Cartões](#55-cartoes)
- [5.6. Convites administrativos](#56-convites-administrativos)
- [5.7. Estados de contas e clientes](#57-estados-de-contas-e-clientes)
- [6. Fluxos funcionais críticos](#6-fluxos-funcionais-criticos)
- [6.1. Fluxo de transferência](#61-fluxo-de-transferencia)
- [6.2. Fluxo de empréstimo](#62-fluxo-de-emprestimo)
- [6.3. Fluxo de conta conjunta](#63-fluxo-de-conta-conjunta)
- [6.4. Fluxo de encerramento de conta](#64-fluxo-de-encerramento-de-conta)
- [7. Regras gerais de integridade](#7-regras-gerais-de-integridade)
- [8. Critérios gerais de aceite](#8-criterios-gerais-de-aceite)
- [9. Fora do escopo da primeira versão](#9-fora-do-escopo-da-primeira-versao)
- [10. Controle de alterações](#10-controle-de-alteracoes)
- [11. Decisões complementares aprovadas](#11-decisoes-complementares-aprovadas)

## 1. Visão geral

## 1.1. Objetivo

Desenvolver uma aplicação bancária digital fictícia que permita demonstrar funcionalidades comuns de uma fintech, utilizando boas práticas de engenharia de software, segurança, arquitetura e consistência financeira.

O sistema deverá oferecer:

- Gestão de clientes.
- Contas individuais e conjuntas.
- Transferências internas.
- Depósitos e saques simulados.
- Pagamentos fictícios.
- Empréstimos.
- Cartões virtuais.
- Extratos e histórico financeiro.
- Notificações.
- Administração.
- Auditoria e privacidade.

Nenhuma operação deverá movimentar dinheiro real ou integrar instituições financeiras externas.

## 1.2. Perfis de usuário

O sistema terá dois perfis:

**CUSTOMER**
- Cliente do MyBank.
- Acessa sua conta e seus produtos.
- Executa operações financeiras permitidas.
- Gerencia informações pessoais e configurações de segurança.

**ADMIN**
- Administrador do sistema.
- Gerencia clientes, contas, operações e parâmetros.
- Consulta indicadores e auditoria.
- Executa ações administrativas conforme as permissões definidas.

Não haverá perfil SUPPORT.

Todos os administradores terão as mesmas permissões.

## 2. Requisitos funcionais

## RF-001 — Cadastro de clientes

O sistema deverá permitir:

- Cadastro público de clientes.
- Registro de dados pessoais.
- Cadastro de e-mail e senha.
- Validação dos campos obrigatórios.
- Verificação de unicidade do e-mail.
- Verificação de e-mail por fluxo simulado.
- Registro opcional de renda declarada.
- Aceite das condições e políticas aplicáveis.
- Registro da data de criação.

A renda poderá ser informada posteriormente em Perfil → Dados financeiros.

Não será exigido comprovante de renda na primeira versão.

## RF-002 — Autenticação

O sistema deverá permitir:

- Login com e-mail e senha.
- Autenticação utilizando Spring Security.
- Senhas protegidas por Argon2id.
- Emissão de Access Token e Refresh Token.
- Renovação de sessão com rotação do Refresh Token.
- Logout e revogação da sessão.
- Recuperação de senha por fluxo simulado.
- Histórico de autenticação.
- Identificação de sessões e dispositivos.
- Encerramento de sessões pelo cliente.

A autenticação deverá respeitar as regras de validade, armazenamento e proteção de tokens estabelecidas nos ADRs.

## RF-003 — Autenticação em dois fatores

O sistema deverá implementar TOTP.

Para CUSTOMER:
- Ativação opcional.
- Desativação mediante reautenticação.
- Códigos de recuperação de uso único.
- Autenticação adicional em operações sensíveis conforme política de risco.

Para ADMIN:
- TOTP obrigatório.
- Configuração obrigatória no primeiro acesso.
- Acesso ao painel administrativo somente após configuração.
- Não permitir desativação do requisito obrigatório.

Para demonstração em ambiente de desenvolvimento, poderá existir mecanismo isolado de código aleatório de seis dígitos, com validade de dois minutos, uso único e limite de tentativas.

Esse mecanismo não deverá ser utilizado como substituto do TOTP em produção.

## RF-004 — Gestão de clientes

O administrador deverá poder:

- Consultar clientes.
- Pesquisar por identificadores permitidos.
- Visualizar dados cadastrais.
- Consultar situação cadastral.
- Bloquear ou suspender clientes.
- Reativar clientes quando permitido.
- Consultar contas vinculadas.
- Consultar operações relacionadas.
- Consultar histórico de alterações.

Ações administrativas relevantes deverão exigir justificativa e gerar auditoria.

## RF-005 — Gestão de contas bancárias

O sistema deverá permitir:

- Criação de contas individuais.
- Criação de contas conjuntas.
- Geração automática de número de conta.
- Utilização de número numérico de nove dígitos com dígito verificador.
- Identificação interna por UUID.
- Consulta de titulares.
- Convites para inclusão de titulares.
- Aceitação e cancelamento de convites.
- Consulta de participação percentual, quando definida.
- Bloqueio e encerramento de contas.

Cada cliente poderá participar de apenas uma conta ativa.

### Regras de contas conjuntas

- Conta individual: exatamente um titular.
- Conta conjunta: dois ou mais titulares.
- Titulares ativos possuem direitos iguais.
- Qualquer titular ativo pode convidar outro cliente cadastrado.
- Somente o destinatário autenticado poderá aceitar o convite.
- O backend verificará se o destinatário já participa de outra conta ativa.
- Convites terão validade limitada.
- Convites poderão ser cancelados.
- Não será permitida remoção unilateral de titulares.
- A saída voluntária respeitará o número mínimo de titulares.
- O encerramento conjunto exige consentimento de todos os titulares ativos.

A participação percentual será opcional. Quando definida para os titulares, a soma deverá totalizar 100%.

Sem percentuais definidos, o patrimônio permanecerá compartilhado, sem atribuição individual.

## RF-006 — Consulta de saldo

O cliente deverá visualizar:

- Saldo atual.
- Saldo disponível.
- Informações da conta.
- Titulares.
- Participação percentual, quando aplicável.

O saldo deverá refletir os lançamentos efetivamente contabilizados no Ledger.

Operações pendentes não reservarão saldo.

A projeção de saldo deverá ser atualizada atomicamente com o Ledger.

## RF-007 — Extrato financeiro

O sistema deverá permitir:

- Consulta de movimentações.
- Consulta de saldo anterior e atual.
- Visualização de entradas e saídas.
- Data e horário.
- Descrição.
- Categoria.
- Titular responsável pela operação.
- Identificação da operação.
- Consulta detalhada.
- Filtros por período, tipo, categoria e titular.
- Pesquisa por descrição ou identificador.
- Exportação em PDF.
- Exportação em CSV.

Em contas conjuntas, o extrato será compartilhado.

O filtro por titular terá finalidade informativa e não modificará a contabilização ou o patrimônio compartilhado.

## RF-008 — Transferências internas

O sistema deverá permitir transferências entre contas MyBank.

Fluxo:

1. Selecionar a conta de origem.
2. Informar a conta de destino.
3. Validar a existência da conta.
4. Apresentar os dados identificadores do destinatário.
5. Informar o valor.
6. Informar descrição opcional.
7. Apresentar resumo.
8. Solicitar autenticação adicional quando exigida.
9. Confirmar operação.
10. Processar e registrar no Ledger.
11. Apresentar comprovante.

Validações obrigatórias:

- Conta de origem ativa.
- Conta de destino válida e apta a receber.
- Cliente autorizado como titular da origem.
- Valor positivo.
- Saldo suficiente no momento do processamento.
- Idempotência.
- Proteção contra concorrência.
- Consistência das partidas dobradas.

A operação deverá identificar o titular iniciador, inclusive em contas conjuntas.

Não haverá transferências para outros bancos.

## RF-009 — Depósitos simulados

O sistema deverá permitir depósitos fictícios.

- Informar valor.
- Apresentar resumo.
- Confirmar operação.
- Processar depósito.
- Registrar contrapartida contábil.
- Atualizar Ledger.
- Atualizar projeção do saldo.
- Gerar comprovante.
- Registrar histórico.

Não haverá integração com dinheiro físico ou sistemas externos.

## RF-010 — Saques simulados

O sistema deverá permitir saques fictícios.

- Informar valor.
- Validar saldo no processamento.
- Confirmar operação.
- Registrar contrapartida contábil.
- Atualizar Ledger.
- Atualizar projeção do saldo.
- Gerar comprovante.
- Registrar histórico.

Não haverá integração com caixas eletrônicos ou instituições financeiras reais.

## RF-011 — Pagamentos

O sistema deverá oferecer pagamentos de serviços fictícios.

Categorias:

- Água.
- Energia elétrica.
- Internet.
- Telefonia.
- Gás.
- Outros serviços simulados.

Funcionalidades:

- Selecionar categoria.
- Selecionar prestador fictício.
- Informar dados de referência.
- Informar valor.
- Consultar vencimento, quando aplicável.
- Apresentar resumo.
- Confirmar pagamento.
- Agendar pagamento.
- Cancelar agendamento quando permitido.
- Consultar histórico.
- Gerar comprovante.

Não haverá processamento de boletos reais, Pix ou pagamentos externos.

## RF-012 — Empréstimos

O sistema deverá permitir:

- Consultar condições disponíveis.
- Simular empréstimos.
- Informar valor desejado.
- Informar prazo.
- Consultar juros mensais.
- Visualizar parcelas.
- Consultar CET demonstrativo, se aplicável aos parâmetros fictícios.
- Solicitar empréstimo.
- Acompanhar análise.
- Consultar decisão.
- Contratar proposta aprovada.
- Consultar contrato.
- Consultar parcelas.
- Consultar saldo devedor.
- Efetuar pagamentos simulados.
- Consultar histórico de pagamentos.

### Regras de análise

A análise deverá considerar:

- Renda declarada.
- Snapshot da renda no momento da solicitação.
- Empréstimos ativos.
- Parcelas existentes.
- Valor solicitado.
- Prazo.
- Juros.
- Capacidade de pagamento.

O comprometimento máximo será de 30% da renda declarada.

Poderão existir múltiplos empréstimos simultâneos, desde que o comprometimento total respeite o limite.

A análise deverá possuir controle de concorrência.

### Regras contratuais

- Sistema Price.
- Condições aprovadas registradas no contrato.
- Taxa, prazo, valor e método de amortização preservados.
- Aprovação manual com justificativa e auditoria.
- Sem segunda aprovação.
- Proposta aprovada deverá possuir prazo de validade.
- Desembolso deverá ser atômico e contabilizado no Ledger.

Empréstimos ativos continuam sendo obrigações pessoais mesmo após encerramento da conta.

## RF-013 — Cartões virtuais

O sistema deverá permitir:

- Criar cartões virtuais fictícios.
- Consultar cartões.
- Criar múltiplos cartões por cliente.
- Definir limite de gasto.
- Consultar limite disponível.
- Bloquear cartão.
- Reativar cartão bloqueado.
- Cancelar cartão.
- Consultar histórico de compras.
- Consultar estornos.

### Regras

- O limite representa capacidade de gasto, não crédito.
- Compras simuladas debitam imediatamente o saldo bancário.
- Compras consomem o limite do cartão.
- Estornos recompõem saldo e limite.
- Cartão cancelado não poderá ser reativado.
- Não haverá cartão físico, bandeira ou compra real.

## RF-014 — Notificações

O sistema deverá oferecer:

- Notificações internas.
- E-mail simulado.
- Push no navegador.
- Histórico de notificações.
- Preferências por canal.
- Preferências por tipo de evento.
- Marcação como lida.
- Filtros.
- Registro de tentativas.
- Registro de falhas.
- Controle contra duplicidade.

O e-mail será simulado e registrado no ambiente de desenvolvimento, sem envio real.

Push dependerá de autorização do usuário e utilizará a API de notificações do navegador e Service Worker.

Notificações críticas não poderão ser desabilitadas quando a política de segurança exigir sua entrega.

Não incluir dados sensíveis desnecessários nas mensagens.

## RF-015 — Perfil do cliente

O cliente deverá poder:

- Consultar dados pessoais.
- Atualizar informações permitidas.
- Consultar dados financeiros.
- Informar renda declarada.
- Atualizar renda.
- Consultar data da última atualização.
- Alterar preferências.
- Consultar configurações de segurança.

A renda será opcional no cadastro e poderá ser preenchida posteriormente.

Atualizações futuras não deverão alterar os snapshots registrados em solicitações e contratos anteriores.

## RF-016 — Segurança do cliente

O cliente deverá poder:

- Alterar senha.
- Configurar TOTP.
- Consultar sessões.
- Encerrar sessões.
- Consultar histórico de acessos.
- Consultar alertas de segurança.
- Gerenciar códigos de recuperação.

Ações sensíveis poderão exigir senha ou segundo fator.

## RF-017 — Dashboard do cliente

O dashboard deverá apresentar:

- Saldo.
- Patrimônio compartilhado ou individual.
- Receitas.
- Despesas.
- Evolução financeira.
- Gráficos.
- Movimentações recentes.
- Situação de empréstimos.
- Pagamentos pendentes.
- Cartões.
- Notificações.

Os dados deverão representar informações efetivamente registradas no sistema.

## RF-018 — Administração do sistema

O administrador deverá possuir acesso a:

- Dashboard administrativo.
- Clientes.
- Contas.
- Transações.
- Ledger.
- Pagamentos.
- Empréstimos.
- Cartões.
- Usuários.
- Configurações.
- Auditoria.
- Parâmetros do sistema.

Poderá consultar, bloquear, suspender, reativar ou encerrar recursos quando as regras permitirem.

Todas as operações sob `/admin/**` exigem usuário autenticado com perfil ADMIN, sessão administrativa com 2FA concluído e autorização validada pelo backend. O esquema Bearer representa autenticação e não substitui a autorização por perfil. A consulta de solicitações de empréstimo por `GET /admin/loans` deve ser registrada na auditoria e não exige reautenticação contextual.

Não poderá alterar diretamente saldos.

Correções financeiras deverão ocorrer por estorno ou compensação.

### Gestão de administradores

- Primeiro administrador criado por bootstrap.
- Demais administradores convidados.
- Convite de uso único e validade limitada.
- Destinatário define a própria senha.
- Todos os administradores possuem permissões iguais.
- Não permitir desativação do último administrador ativo.

Ações críticas exigem confirmação adicional, justificativa e auditoria.

Não haverá segunda aprovação.

## RF-019 — Dashboard administrativo

Apresentar indicadores como:

- Quantidade de clientes.
- Quantidade de contas.
- Contas individuais e conjuntas.
- Depósitos.
- Saques.
- Transferências.
- Pagamentos.
- Empréstimos.
- Inadimplência simulada.
- Evolução financeira.
- Patrimônio financeiro total registrado.

Distinguir patrimônio dos clientes, saldos contabilizados e patrimônio ou caixa da instituição fictícia.

Indicadores demonstrativos deverão ser identificados como dados fictícios.

## RF-020 — Auditoria

O sistema deverá registrar eventos relevantes, incluindo:

- Login e tentativas de autenticação.
- Alterações de permissões.
- Ações administrativas.
- Bloqueios e encerramentos.
- Aprovações e rejeições.
- Operações financeiras relevantes.
- Consultas a dados sensíveis.
- Alterações de parâmetros.
- Operações de privacidade.

Os registros deverão conter informações suficientes para rastreabilidade, sem expor segredos ou dados desnecessários.

## RF-021 — Privacidade e LGPD

O sistema deverá permitir:

- Registro de solicitações de titulares.
- Consulta de dados pessoais.
- Solicitação de correção.
- Solicitação de anonimização ou eliminação quando permitida.
- Aplicação de políticas de retenção.
- Registro das decisões.
- Auditoria das operações.

A exclusão de dados deverá respeitar obrigações legais, finalidade e base legal.

Histórico financeiro, Ledger e auditoria não poderão ser apagados por operações administrativas comuns.

## 3. Requisitos não funcionais

## RNF-001 — Arquitetura

- Arquitetura Hexagonal.
- Monólito Modular.
- Separação entre domínio, aplicação e infraestrutura.
- Baixo acoplamento.
- Alta coesão.
- Limites modulares verificáveis.

## RNF-002 — Segurança

- Spring Security.
- JWT com Access e Refresh Token.
- Argon2id.
- AES-256-GCM.
- TOTP.
- Rate limiting.
- Validação de entradas.
- Proteção contra XSS.
- Proteção contra CSRF conforme estratégia de cookies.
- CSP.
- Controle de autorização no backend.
- Proteção contra enumeração de usuários.
- Não exposição de segredos.

## RNF-003 — Consistência financeira

- Partidas dobradas.
- Transações ACID.
- Idempotência.
- Controle de concorrência.
- Lançamentos imutáveis.
- Estorno e compensação.
- Reconciliação de saldos.
- Atualização atômica da projeção.

## RNF-004 — Precisão monetária

- BigDecimal no Java.
- NUMERIC(19,2) no PostgreSQL.
- BRL exclusivamente.
- Regras de arredondamento documentadas.

## RNF-005 — Desempenho

- Consultas paginadas quando aplicável.
- Índices adequados.
- Evitar consultas N+1.
- Processar tarefas secundárias de forma assíncrona.
- Evitar bloqueios desnecessários.
- Monitorar consultas e operações críticas.

Não estabelecer metas artificiais de desempenho sem medição e justificativa.

## RNF-006 — Disponibilidade e recuperação

- Backups diários.
- RPO de até 24 horas.
- RTO de até 4 horas.
- Retenção definida.
- Backups criptografados.
- Testes mensais de restauração.

## RNF-007 — Manutenibilidade

- Código legível.
- Padrões consistentes.
- Documentação atualizada.
- Testes automatizados.
- Dependências justificadas.
- Separação de responsabilidades.
- Ausência de duplicação desnecessária.

## RNF-008 — Testabilidade

- Testes unitários.
- Testes de integração.
- Testes arquiteturais.
- Testes E2E.
- Testes de segurança.
- Testes de concorrência.
- Testes de idempotência.

## RNF-009 — Portabilidade e reprodução

- Docker.
- Docker Compose.
- Dev Containers.
- Maven Wrapper.
- `.nvmrc`.
- `package-lock.json`.
- `.env.example`.
- Configurações documentadas.

## RNF-010 — Usabilidade

- Interface responsiva.
- Design System.
- Tema escuro.
- Feedback visual.
- Confirmações para operações críticas.
- Estados de carregamento, vazio, erro e sucesso.
- Navegação intuitiva.

## RNF-011 — Acessibilidade

- Navegação por teclado.
- Contraste adequado.
- Labels acessíveis.
- Semântica correta.
- Feedback compreensível.
- Compatibilidade com tecnologias assistivas.

## RNF-012 — Privacidade

- Minimização de dados.
- Controle de acesso.
- Retenção documentada.
- Anonimização quando permitida.
- Auditoria.
- Proteção de informações sensíveis.

## 4. Matriz de permissões

Legenda:

- **SIM:** permitido.
- **CONDICIONAL:** permitido conforme regras, estado ou titularidade.
- **NÃO:** não permitido.

| Funcionalidade | CUSTOMER | ADMIN |
| --- | --- | --- |
| Cadastro público | SIM | NÃO |
| Login | SIM | SIM |
| Gerenciar própria sessão | SIM | SIM |
| Consultar próprio perfil | SIM | CONDICIONAL |
| Atualizar próprio perfil | SIM | CONDICIONAL |
| Informar renda própria | SIM | NÃO |
| Consultar própria conta | SIM | CONDICIONAL |
| Criar conta | CONDICIONAL | CONDICIONAL |
| Convidar titular | CONDICIONAL | NÃO |
| Aceitar convite | CONDICIONAL | NÃO |
| Consultar extrato | CONDICIONAL | CONDICIONAL |
| Transferir | CONDICIONAL | NÃO |
| Depositar | CONDICIONAL | NÃO |
| Sacar | CONDICIONAL | NÃO |
| Pagar serviços fictícios | CONDICIONAL | NÃO |
| Solicitar empréstimo | CONDICIONAL | NÃO |
| Analisar empréstimo | NÃO | SIM |
| Contratar empréstimo aprovado | CONDICIONAL | NÃO |
| Gerenciar cartões próprios | CONDICIONAL | NÃO |
| Consultar notificações próprias | SIM | SIM |
| Consultar outros clientes | NÃO | SIM |
| Administrar contas de clientes | NÃO | SIM |
| Consultar Ledger | CONDICIONAL | SIM |
| Alterar saldo diretamente | NÃO | NÃO |
| Executar estornos administrativos | NÃO | CONDICIONAL |
| Gerenciar administradores | NÃO | SIM |
| Consultar auditoria | NÃO | SIM |
| Alterar parâmetros do sistema | NÃO | CONDICIONAL |
| Consultar dados sensíveis de terceiros | NÃO | CONDICIONAL |

**Observação:** CUSTOMER somente poderá operar contas nas quais seja titular ativo. ADMIN deverá possuir autorização explícita para cada ação administrativa. A matriz não substitui as validações de domínio, titularidade e estado.

## 5. Máquinas de estados

## 5.1. Transações

Estados:

- PENDING
- PROCESSING
- COMPLETED
- FAILED
- REJECTED
- CANCELLED

Transições:

```text
PENDING
 ├── PROCESSING
 └── CANCELLED

PROCESSING
 ├── COMPLETED
 ├── FAILED
 └── REJECTED

```

COMPLETED, FAILED, REJECTED e CANCELLED são estados finais.

## 5.2. Pagamentos

Estados:

- PENDING
- SCHEDULED
- PROCESSING
- COMPLETED
- FAILED
- REJECTED
- CANCELLED

Transições:

```text
PENDING
 ├── SCHEDULED
 ├── PROCESSING
 └── CANCELLED

SCHEDULED
 ├── PROCESSING
 └── CANCELLED

PROCESSING
 ├── COMPLETED
 ├── FAILED
 └── REJECTED

```

O cancelamento deverá respeitar o estado e o momento do processamento.

## 5.3. Empréstimos

Estados:

- REQUESTED
- UNDER_REVIEW
- APPROVED
- REJECTED
- CONTRACTED
- ACTIVE
- PAID_OFF
- CANCELLED

Transições:

```text
REQUESTED
 ├── UNDER_REVIEW
 ├── REJECTED
 └── CANCELLED

UNDER_REVIEW
 ├── APPROVED
 └── REJECTED

APPROVED
 ├── CONTRACTED
 └── CANCELLED

CONTRACTED
 └── ACTIVE

ACTIVE
 └── PAID_OFF

```

A aprovação terá prazo de validade.

O contrato não poderá ser cancelado diretamente depois de ativo.

As parcelas possuirão estados próprios:

- PENDING
- PAID
- OVERDUE
- CANCELLED

Transição de parcela:

```text
PENDING
 ├── PAID
 ├── OVERDUE
 └── CANCELLED

OVERDUE
 └── PAID

```

## 5.4. Convites de titulares

Estados:

- PENDING
- ACCEPTED
- EXPIRED
- CANCELLED

```text
PENDING
 ├── ACCEPTED
 ├── EXPIRED
 └── CANCELLED

```

## 5.5. Cartões

Estados:

- ACTIVE
- BLOCKED
- CANCELLED

```text
ACTIVE
 ├── BLOCKED
 └── CANCELLED

BLOCKED
 ├── ACTIVE
 └── CANCELLED

```

CANCELLED é definitivo.

## 5.6. Convites administrativos

O convite deverá possuir:

- Identificador único.
- Destinatário.
- Data de criação.
- Data de validade.
- Estado.
- Identificação do administrador responsável.

Estados:

- PENDING
- ACCEPTED
- EXPIRED
- CANCELLED

O convite será de uso único. O destinatário definirá sua senha durante a aceitação.

## 5.7. Estados de contas e clientes

A especificação funcional exige distinguir contas e clientes ativos, bloqueados, suspensos e encerrados.

As transições detalhadas de estados de contas e clientes serão definidas na etapa de modelagem dos fluxos e diagramas, antes da implementação.

## 6. Fluxos funcionais críticos

## 6.1. Fluxo de transferência

1. Cliente autenticado solicita transferência.
2. Backend valida a sessão.
3. Backend valida titularidade e estado da conta.
4. Backend valida destino e valor.
5. Backend registra solicitação idempotente.
6. Sistema exige autenticação adicional quando aplicável.
7. Operação passa para processamento.
8. Backend bloqueia as contas necessárias de forma determinística.
9. Saldo é revalidado.
10. Ledger registra débitos e créditos.
11. Projeções de saldo são atualizadas atomicamente.
12. Operação é concluída.
13. Evento de notificação é registrado na Outbox.
14. Comprovante fica disponível.

Em caso de falha, nenhuma movimentação parcial poderá permanecer contabilizada.

## 6.2. Fluxo de empréstimo

1. Cliente informa valor e prazo.
2. Sistema apresenta simulação.
3. Cliente solicita empréstimo.
4. Sistema registra snapshot da renda e condições.
5. Solicitação passa por análise.
6. Administrador analisa e registra justificativa.
7. Solicitação é aprovada ou rejeitada.
8. Cliente contrata proposta aprovada dentro da validade.
9. Contrato registra condições definitivas.
10. Desembolso é realizado atomicamente.
11. Ledger registra movimentação.
12. Parcelas ficam disponíveis.
13. Pagamentos atualizam o contrato e o Ledger.

## 6.3. Fluxo de conta conjunta

1. Cliente autenticado solicita convite.
2. Backend valida titularidade e conta.
3. Backend valida o destinatário.
4. Convite é criado com validade.
5. Destinatário autentica.
6. Destinatário aceita o convite.
7. Backend verifica se ainda pode participar de uma conta ativa.
8. Backend valida quantidade mínima e demais regras.
9. Participação é registrada.
10. Histórico da conta é atualizado.

## 6.4. Fluxo de encerramento de conta

1. Solicitação de encerramento.
2. Validação dos titulares e consentimentos.
3. Verificação de saldo.
4. Verificação de operações pendentes.
5. Verificação de cartões ativos.
6. Verificação das obrigações financeiras relacionadas.
7. Bloqueio ou cancelamento dos cartões.
8. Encerramento da conta quando todos os critérios forem atendidos.
9. Preservação do histórico financeiro.
10. Registro de auditoria.

O encerramento não poderá apagar obrigações de empréstimos ativos.

## 7. Regras gerais de integridade

1. Toda operação financeira deverá possuir identificador único.
2. Operações repetidas com a mesma chave idempotente não poderão gerar movimentações duplicadas.
3. O Ledger deverá manter partidas dobradas.
4. Nenhuma operação poderá gerar saldo inconsistente.
5. O backend deverá validar o estado antes de qualquer transição.
6. A autorização deverá ser validada em todas as operações protegidas.
7. Dados financeiros não poderão ser modificados diretamente por administradores.
8. Alterações relevantes deverão possuir histórico.
9. Operações concluídas não poderão ser simplesmente apagadas.
10. Falhas financeiras deverão possuir rastreabilidade.
11. Eventos assíncronos não poderão substituir a consistência transacional do Ledger.
12. A interface não poderá ser considerada fonte de verdade para permissões, saldos ou estados.

## 8. Critérios gerais de aceite

O sistema será considerado funcionalmente aderente quando:

- Todos os requisitos implementados estiverem associados a testes.
- Os perfis e permissões forem respeitados.
- As regras de titularidade forem aplicadas no backend.
- As máquinas de estados impedirem transições inválidas.
- Operações financeiras respeitarem partidas dobradas.
- Idempotência e concorrência forem verificadas.
- O histórico financeiro for preservado.
- As condições de empréstimos forem respeitadas.
- O frontend consumir os contratos documentados.
- As operações administrativas forem auditadas.
- Os controles de segurança forem testados.
- A documentação refletir o comportamento implementado.

## 9. Fora do escopo da primeira versão

Não implementar:

- Transferências bancárias externas.
- Pix real.
- Boletos reais.
- Pagamentos reais.
- Integrações com bancos.
- Cartões físicos.
- Bandeiras de cartão.
- Compras reais.
- Operações em moeda estrangeira.
- Câmbio.
- Cotações.
- Integração real com bureaus de crédito.
- Comprovação externa de renda.
- Envio real de e-mails.
- Microsserviços.
- Kafka ou RabbitMQ.
- Operações financeiras reais.

## 10. Controle de alterações

Esta especificação deverá ser atualizada quando houver mudanças aprovadas nos requisitos ou nas regras de negócio.

Toda alteração relevante deverá:

1. Identificar o requisito afetado.
2. Apresentar justificativa.
3. Avaliar impactos na arquitetura.
4. Avaliar impactos no banco de dados.
5. Avaliar impactos nos contratos da API.
6. Atualizar os testes e documentos correspondentes.
7. Receber aprovação do usuário antes da implementação.

## 11. Decisões complementares aprovadas

Conforme a consolidação técnica complementar v1.1:

- CPF é obrigatório, validado quanto a formato e dígitos verificadores, e é o único documento de identificação aceito na primeira versão. A unicidade deve ser protegida sem expor o CPF.
- As condições efetivamente aprovadas de cada empréstimo contratado formam snapshot imutável, preservado após quitação e independente de alterações futuras das configurações.
- Estornos administrativos são integrais, exclusivos de ADMIN, com 2FA, reautenticação, justificativa, confirmação, auditoria, idempotência e limites configuráveis. A correção ocorre por lançamentos compensatórios; lançamentos originais do Ledger não são alterados.
- Reprocessamentos administrativos da Outbox preservam o histórico de tentativas, não modificam o payload original, e são idempotentes, auditados e protegidos contra concorrência.
- `FAILED` é o estado terminal oficial e único elegível para reprocessamento, após validação dos demais critérios. `DEAD_LETTER` descreve somente o encaminhamento ou armazenamento operacional de eventos esgotados, não um estado alternativo.
- `PROCESSED` representa o processamento concluído com sucesso na Outbox; `FAILED` representa falha terminal elegível a reprocessamento, condicionado às demais validações e autorizações. `DEAD_LETTER` permanece mecanismo operacional, não estado do domínio.
