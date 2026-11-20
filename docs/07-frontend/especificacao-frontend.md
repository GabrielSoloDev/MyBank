# MyBank — Especificação Frontend

<aside class="danger-box" role="note" aria-label="Segurança de sessão no frontend">
<strong>Regra de segurança.</strong>
<p>O Access Token permanece em memória. Não armazenar tokens de autenticação em <code>localStorage</code> ou <code>sessionStorage</code>; o backend continua responsável por validar autenticação, autorização e titularidade.</p>
</aside>

| Campo | Valor |
| --- | --- |
| Identificador | MB-FE-001 |
| Classe | Documento oficial da biblioteca MyBank |
| Original preservado | [MyBank — Especificação Frontend.md](../99-fontes-originais/MyBank%20—%20Especificação%20Frontend.md) |
| Projeto | MyBank |
| Documento | Especificação Técnica do Frontend |
| Versão | 1.0 — Reconstruída a partir das decisões aprovadas |
| Status | Referência para desenvolvimento |
| Slogan | Seu dinheiro. Suas possibilidades. |

---

## Sumário

- [1. Objetivo](#1-objetivo)
- [2. Tecnologias e ferramentas](#2-tecnologias-e-ferramentas)
- [2.1. Stack principal](#21-stack-principal)
- [2.2. Testes](#22-testes)
- [2.3. Gerenciamento de dependências](#23-gerenciamento-de-dependencias)
- [3. Arquitetura do frontend](#3-arquitetura-do-frontend)
- [3.1. Organização arquitetural](#31-organizacao-arquitetural)
- [3.2. Separação por áreas](#32-separacao-por-areas)
- [3.3. Estrutura sugerida](#33-estrutura-sugerida)
- [4. Identidade visual e design system](#4-identidade-visual-e-design-system)
- [4.1. Conceito visual](#41-conceito-visual)
- [4.2. Paleta de cores](#42-paleta-de-cores)
- [4.3. Elementos visuais](#43-elementos-visuais)
- [4.4. Tipografia](#44-tipografia)
- [4.5. Componentes do design system](#45-componentes-do-design-system)
- [5. Navegação e rotas](#5-navegacao-e-rotas)
- [5.1. Área pública](#51-area-publica)
- [5.2. Área do cliente](#52-area-do-cliente)
- [5.3. Área administrativa](#53-area-administrativa)
- [5.4. Controle de navegação](#54-controle-de-navegacao)
- [6. Área pública](#6-area-publica)
- [6.1. Página inicial](#61-pagina-inicial)
- [6.2. Login](#62-login)
- [6.3. Cadastro](#63-cadastro)
- [6.4. Recuperação de senha](#64-recuperacao-de-senha)
- [7. Área do cliente](#7-area-do-cliente)
- [7.1. Dashboard](#71-dashboard)
- [7.2. Perfil do cliente](#72-perfil-do-cliente)
- [7.3. Contas bancárias](#73-contas-bancarias)
- [7.4. Contas conjuntas](#74-contas-conjuntas)
- [7.5. Extrato](#75-extrato)
- [7.6. Transferências](#76-transferencias)
- [7.7. Depósitos](#77-depositos)
- [7.8. Saques](#78-saques)
- [7.9. Pagamentos](#79-pagamentos)
- [7.10. Empréstimos](#710-emprestimos)
- [7.11. Cartões virtuais](#711-cartoes-virtuais)
- [7.12. Notificações](#712-notificacoes)
- [7.13. Segurança e sessões](#713-seguranca-e-sessoes)
- [8. Área administrativa](#8-area-administrativa)
- [8.1. Dashboard administrativo](#81-dashboard-administrativo)
- [8.2. Gestão de clientes](#82-gestao-de-clientes)
- [8.3. Gestão de contas](#83-gestao-de-contas)
- [8.4. Gestão de transações](#84-gestao-de-transacoes)
- [8.5. Estornos administrativos](#85-estornos-administrativos)
- [8.6. Gestão de empréstimos](#86-gestao-de-emprestimos)
- [8.7. Gestão de administradores](#87-gestao-de-administradores)
- [8.8. Auditoria](#88-auditoria)
- [8.9. Configurações do sistema](#89-configuracoes-do-sistema)
- [8.10. Outbox e eventos](#810-outbox-e-eventos)
- [8.11. Privacidade](#811-privacidade)
- [9. Autenticação e segurança](#9-autenticacao-e-seguranca)
- [9.1. Integração com autenticação](#91-integracao-com-autenticacao)
- [9.2. Armazenamento de tokens](#92-armazenamento-de-tokens)
- [9.3. Renovação de sessão](#93-renovacao-de-sessao)
- [9.4. Login com 2FA](#94-login-com-2fa)
- [9.5. Administradores](#95-administradores)
- [9.6. Autorização](#96-autorizacao)
- [9.7. Proteções adicionais](#97-protecoes-adicionais)
- [10. Integração com a API REST](#10-integracao-com-a-api-rest)
- [10.1. Comunicação](#101-comunicacao)
- [10.2. Camada de serviços](#102-camada-de-servicos)
- [10.3. Gerenciamento de estado remoto](#103-gerenciamento-de-estado-remoto)
- [10.4. Estado local](#104-estado-local)
- [10.5. Erros](#105-erros)
- [10.6. Idempotência](#106-idempotencia)
- [11. Formulários e validação](#11-formularios-e-validacao)
- [11.1. Bibliotecas](#111-bibliotecas)
- [11.2. Padrões](#112-padroes)
- [11.3. Valores financeiros](#113-valores-financeiros)
- [12. Responsividade](#12-responsividade)
- [13. Acessibilidade](#13-acessibilidade)
- [14. Desempenho e experiência do usuário](#14-desempenho-e-experiencia-do-usuario)
- [15. Estados e feedback visual](#15-estados-e-feedback-visual)
- [16. Testes e qualidade](#16-testes-e-qualidade)
- [16.1. Vitest](#161-vitest)
- [16.2. React Testing Library](#162-react-testing-library)
- [16.3. Playwright](#163-playwright)
- [16.4. Segurança frontend](#164-seguranca-frontend)
- [17. Variáveis de ambiente](#17-variaveis-de-ambiente)
- [18. Padrões de desenvolvimento](#18-padroes-de-desenvolvimento)
- [19. Limites de responsabilidade](#19-limites-de-responsabilidade)
- [20. Critérios de aceite](#20-criterios-de-aceite)
- [21. Diretriz final](#21-diretriz-final)
- [22. Decisões complementares aprovadas — CPF e Outbox](#22-decisoes-complementares-aprovadas-cpf-e-outbox)

## 1. Objetivo

Definir os requisitos funcionais, técnicos, visuais e arquiteturais do frontend do MyBank, estabelecendo padrões para desenvolvimento de uma interface moderna, responsiva, acessível, segura e integrada ao backend.

O frontend será responsável pela apresentação das informações, interação com o usuário, validação preliminar de formulários e comunicação com a API REST.

O frontend não será responsável por regras financeiras, decisões de autorização ou validações de segurança definitivas. Essas responsabilidades pertencem ao backend.

## 2. Tecnologias e ferramentas

## 2.1. Stack principal

- React.
- JavaScript.
- Vite.
- React Router.
- Tailwind CSS.
- TanStack Query.
- React Hook Form.
- Zod.
- Axios.
- Recharts.
- Lucide.

## 2.2. Testes

- Vitest.
- React Testing Library.
- Playwright.

## 2.3. Gerenciamento de dependências

- Node.js LTS.
- npm.
- `.nvmrc` para fixação da versão do Node.js.
- `package-lock.json` versionado.
- Dependências com versões controladas.
- Proibição de dependências desnecessárias.
- Verificação de compatibilidade e vulnerabilidades.

## 3. Arquitetura do frontend

## 3.1. Organização arquitetural

O frontend será desenvolvido como uma aplicação React organizada por funcionalidades e responsabilidades.

A organização deverá favorecer:

- Separação de responsabilidades.
- Componentes reutilizáveis.
- Baixo acoplamento.
- Alta coesão.
- Facilidade de manutenção.
- Testabilidade.
- Escalabilidade estrutural.
- Consistência visual.

## 3.2. Separação por áreas

A aplicação será dividida em três áreas principais:

1. Área pública.
2. Área do cliente.
3. Área administrativa.

Cada área deverá possuir seus próprios layouts, navegação, páginas e componentes específicos, reutilizando elementos compartilhados quando apropriado.

## 3.3. Estrutura sugerida

```text
frontend/
├── public/
├── src/
│   ├── app/
│   │   ├── router/
│   │   ├── providers/
│   │   └── config/
│   │
│   ├── assets/
│   │
│   ├── components/
│   │   ├── ui/
│   │   ├── forms/
│   │   ├── feedback/
│   │   ├── navigation/
│   │   └── charts/
│   │
│   ├── layouts/
│   │   ├── PublicLayout.jsx
│   │   ├── CustomerLayout.jsx
│   │   └── AdminLayout.jsx
│   │
│   ├── features/
│   │   ├── auth/
│   │   ├── customers/
│   │   ├── accounts/
│   │   ├── transactions/
│   │   ├── payments/
│   │   ├── loans/
│   │   ├── cards/
│   │   ├── notifications/
│   │   ├── admin/
│   │   ├── audit/
│   │   └── privacy/
│   │
│   ├── hooks/
│   ├── services/
│   ├── lib/
│   ├── utils/
│   ├── schemas/
│   ├── styles/
│   └── main.jsx
│
├── tests/
├── .env.example
├── .nvmrc
├── package.json
├── package-lock.json
└── vite.config.js

```

A estrutura definitiva poderá ser ajustada durante a organização do repositório, sem alterar as responsabilidades arquiteturais estabelecidas.

## 4. Identidade visual e design system

## 4.1. Conceito visual

O MyBank deverá apresentar uma identidade visual de fintech premium, tecnológica, confiável, moderna e centrada no cliente.

Características:

- Interface exclusivamente dark.
- Aparência profissional e sofisticada.
- Hierarquia visual clara.
- Navegação intuitiva.
- Componentes consistentes.
- Uso moderado de efeitos visuais.
- Animações suaves.
- Feedback visual para ações.
- Ênfase em legibilidade e transparência financeira.

Não deverá existir alternância para tema claro.

## 4.2. Paleta de cores

| Aplicação | Cor |
| --- | --- |
| Fundo principal | `#080B12` |
| Superfícies e cards | `#111827` |
| Ação principal | `#176BFF` |
| Destaque | `#38BDF8` |
| Texto principal | `#F8FAFC` |
| Texto secundário | `#94A3B8` |
| Sucesso | `#22C55E` |
| Erro | `#EF4444` |
| Alerta | `#F59E0B` |

As cores deverão ser centralizadas em tokens do design system, evitando valores repetidos diretamente nos componentes.

## 4.3. Elementos visuais

- Gradientes em áreas de destaque.
- Brilhos azuis discretos.
- Glassmorphism moderado.
- Cards com bordas sutis.
- Sombras controladas.
- Ícones consistentes.
- Gráficos modernos.
- Transições suaves.
- Estados visuais de carregamento, sucesso, erro e desabilitado.

Os efeitos não deverão prejudicar desempenho, acessibilidade ou legibilidade.

## 4.4. Tipografia

A tipografia deverá priorizar:

- Leitura confortável.
- Hierarquia entre títulos, subtítulos e textos.
- Clareza em valores financeiros.
- Consistência entre páginas.
- Boa visualização em telas menores.

## 4.5. Componentes do design system

Criar componentes reutilizáveis para:

- Botões.
- Campos de entrada.
- Seletores.
- Checkboxes.
- Radio buttons.
- Modais.
- Dropdowns.
- Tooltips.
- Cards.
- Badges de status.
- Tabelas.
- Paginação.
- Abas.
- Breadcrumbs.
- Menus.
- Alertas.
- Toasts.
- Skeletons.
- Indicadores de carregamento.
- Estados vazios.
- Confirmações de operações.
- Gráficos.
- Elementos de navegação.

Os componentes deverão apresentar estados visuais consistentes.

## 5. Navegação e rotas

## 5.1. Área pública

Rotas previstas:

- `/`
- `/login`
- `/register`
- `/forgot-password`
- `/reset-password`
- `/verify-email`
- `/terms`
- `/privacy`

## 5.2. Área do cliente

Rotas previstas:

- `/app/dashboard`
- `/app/profile`
- `/app/security`
- `/app/accounts`
- `/app/accounts/:accountId`
- `/app/accounts/:accountId/statement`
- `/app/transfers`
- `/app/deposits`
- `/app/withdrawals`
- `/app/payments`
- `/app/payments/:paymentId`
- `/app/loans`
- `/app/loans/simulate`
- `/app/loans/:loanId`
- `/app/cards`
- `/app/cards/:cardId`
- `/app/notifications`
- `/app/account-invitations`
- `/app/sessions`
- `/app/privacy`

## 5.3. Área administrativa

Rotas previstas:

- `/admin/dashboard`
- `/admin/customers`
- `/admin/customers/:customerId`
- `/admin/accounts`
- `/admin/accounts/:accountId`
- `/admin/transactions`
- `/admin/transactions/:transactionId`
- `/admin/reversals`
- `/admin/loans`
- `/admin/loans/:loanId`
- `/admin/cards`
- `/admin/administrators`
- `/admin/invitations`
- `/admin/audit`
- `/admin/settings`
- `/admin/outbox`
- `/admin/privacy`

## 5.4. Controle de navegação

- Rotas organizadas por área.
- Layout específico para cada área.
- Redirecionamento conforme autenticação.
- Tratamento de rotas inexistentes.
- Tratamento de sessão expirada.
- Menus condicionais conforme perfil.
- Proteção de navegação para melhorar a experiência.

A proteção de rotas no frontend é apenas uma camada de UX. A autorização definitiva será realizada pelo backend em todas as requisições.

## 6. Área pública

## 6.1. Página inicial

Apresentar a identidade MyBank e suas funcionalidades simuladas.

Elementos:

- Logo e identidade visual.
- Slogan.
- Apresentação do banco digital.
- Destaques das funcionalidades.
- Benefícios da plataforma.
- Chamadas para cadastro e login.
- Rodapé institucional.
- Links de privacidade e termos.

## 6.2. Login

- Identificação do usuário.
- Senha.
- Exibição ou ocultação da senha.
- Validação dos campos.
- Mensagens de erro.
- Controle de tentativas.
- Fluxo de autenticação com 2FA quando necessário.

## 6.3. Cadastro

- Formulário de cadastro.
- Validação de campos.
- Confirmação dos dados.
- Aceite dos termos aplicáveis.
- Feedback do resultado.
- Orientação para verificação de e-mail.

## 6.4. Recuperação de senha

- Solicitação de recuperação.
- Confirmação genérica para evitar enumeração de usuários.
- Redefinição por token.
- Validação da nova senha.
- Feedback de conclusão.

## 7. Área do cliente

## 7.1. Dashboard

Apresentar uma visão geral da situação financeira do cliente.

Elementos:

- Saudação e identificação.
- Saldo da conta.
- Resumo de movimentações.
- Últimas transações.
- Atalhos para operações.
- Resumo de pagamentos.
- Resumo de empréstimos.
- Cartões virtuais.
- Notificações recentes.
- Gráficos de movimentação.

Os valores deverão ser obtidos da API.

## 7.2. Perfil do cliente

- Visualização dos dados cadastrais.
- Atualização de informações permitidas.
- Visualização do estado cadastral.
- Informações de segurança.
- Dados pessoais protegidos.

Alterações sensíveis deverão respeitar os requisitos de reautenticação.

## 7.3. Contas bancárias

- Listagem das contas.
- Visualização dos detalhes.
- Número da conta.
- Tipo de conta.
- Estado da conta.
- Saldo.
- Titulares.
- Participações percentuais, quando definidas.
- Extrato.
- Convites para conta conjunta.
- Solicitação de encerramento.

A interface deverá distinguir contas individuais e conjuntas.

## 7.4. Contas conjuntas

- Visualização dos titulares.
- Convite de clientes cadastrados.
- Aceitação de convite.
- Cancelamento de convite.
- Visualização do estado dos convites.
- Visualização de participações percentuais, quando existentes.
- Solicitação de saída voluntária.
- Solicitação de encerramento conjunto.

A interface deverá explicar que o encerramento conjunto depende do consentimento de todos os titulares ativos.

O frontend não poderá permitir remoção unilateral de titulares.

## 7.5. Extrato

- Listagem de movimentações.
- Paginação.
- Ordenação.
- Filtro por período.
- Filtro por tipo.
- Filtro por estado.
- Filtro por valor.
- Filtro por descrição.
- Visualização dos detalhes.
- Identificação de entradas e saídas.
- Exibição de comprovantes disponíveis.

Os filtros deverão respeitar as regras definidas nos contratos da API.

## 7.6. Transferências

- Seleção da conta de origem.
- Identificação da conta de destino.
- Informação do valor.
- Descrição opcional.
- Revisão dos dados.
- Confirmação.
- Reautenticação ou 2FA quando exigidos.
- Exibição do resultado.
- Disponibilização do comprovante.

As transferências serão exclusivamente entre contas MyBank.

## 7.7. Depósitos

- Seleção da conta.
- Informações do depósito simulado.
- Valor.
- Confirmação.
- Resultado da operação.
- Comprovante.

Não haverá integração com instituições financeiras externas.

## 7.8. Saques

- Seleção da conta.
- Valor.
- Revisão.
- Confirmação.
- Reautenticação quando exigida.
- Resultado.
- Comprovante.

Os saques serão simulados.

## 7.9. Pagamentos

- Listagem de prestadores fictícios.
- Categorias de serviços.
- Água.
- Energia.
- Internet.
- Telefonia.
- Gás.
- Outros serviços simulados.
- Seleção da conta.
- Valor.
- Data, quando aplicável.
- Agendamento.
- Revisão e confirmação.
- Histórico de pagamentos.
- Detalhes e comprovantes.

Não haverá pagamento real de boletos, Pix ou serviços externos.

## 7.10. Empréstimos

- Listagem de empréstimos.
- Simulação.
- Informações de valor, prazo e juros.
- Visualização das parcelas.
- Solicitação de empréstimo.
- Acompanhamento da análise.
- Visualização da proposta aprovada.
- Contratação.
- Acompanhamento do contrato.
- Histórico de pagamentos.
- Pagamento integral de parcelas.
- Visualização de parcelas vencidas e quitadas.

A simulação apresentada no frontend será indicativa. O backend será responsável pelo cálculo oficial e pelas condições contratuais.

A interface não deverá permitir informar livremente o valor de pagamento de uma parcela: o backend determinará o valor integral devido.

## 7.11. Cartões virtuais

- Listagem de cartões.
- Criação de cartão virtual.
- Visualização de dados demonstrativos.
- Limite de gasto.
- Estado do cartão.
- Bloqueio.
- Reativação quando permitida.
- Cancelamento.
- Histórico de compras.
- Detalhes das compras.

Os cartões são fictícios. Não haverá PAN real, CVV real, bandeira, cartão físico ou compras externas.

As compras simuladas debitam o saldo bancário e consomem o limite de gasto.

## 7.12. Notificações

- Listagem de notificações.
- Indicador de não lidas.
- Marcação como lida.
- Preferências de notificação.
- Histórico.
- Detalhes.

## 7.13. Segurança e sessões

- Visualização de sessões ativas.
- Encerramento de sessões.
- Alteração de senha.
- Configuração de 2FA.
- Exibição de QR Code para configuração TOTP.
- Confirmação da configuração.
- Gerenciamento de códigos de recuperação.
- Recuperação de acesso.

O cliente poderá habilitar 2FA opcionalmente.

## 8. Área administrativa

## 8.1. Dashboard administrativo

- Indicadores gerais.
- Quantidade de clientes.
- Quantidade de contas.
- Transações.
- Pagamentos.
- Empréstimos.
- Cartões.
- Operações pendentes.
- Eventos com falha.
- Indicadores operacionais.

Os indicadores deverão ser fornecidos pela API, sem cálculos financeiros independentes no frontend.

## 8.2. Gestão de clientes

- Listagem.
- Pesquisa.
- Filtros.
- Detalhes.
- Estado cadastral.
- Histórico.
- Bloqueio.
- Suspensão.
- Reativação, quando permitida.
- Desativação conforme regras de negócio.

## 8.3. Gestão de contas

- Listagem.
- Pesquisa.
- Detalhes.
- Titulares.
- Saldo.
- Estado.
- Histórico.
- Bloqueio.
- Suspensão.
- Encerramento conforme regras de negócio.

Não será permitido alterar saldo diretamente pela interface administrativa.

## 8.4. Gestão de transações

- Listagem.
- Filtros.
- Pesquisa.
- Detalhes.
- Estado.
- Histórico.
- Identificação de lançamentos relacionados.
- Consulta de comprovantes.

## 8.5. Estornos administrativos

- Consulta de transações elegíveis.
- Visualização dos critérios de elegibilidade.
- Informações sobre limites configurados.
- Justificativa obrigatória.
- Confirmação adicional.
- Reautenticação.
- 2FA.
- Resultado do estorno.
- Histórico de reversões.

A interface deverá deixar claro que o estorno é integral na primeira versão.

Não deverá permitir:
- Estornar estornos.
- Estornar transações não elegíveis.
- Alterar lançamentos originais.
- Executar estornos sem justificativa.
- Ignorar limites definidos pelo backend.

## 8.6. Gestão de empréstimos

- Listagem de solicitações.
- Filtros.
- Detalhes.
- Análise.
- Visualização da capacidade de pagamento.
- Histórico.
- Aprovação.
- Rejeição.
- Justificativa obrigatória nas decisões administrativas aplicáveis.

Não haverá segunda aprovação.

## 8.7. Gestão de administradores

- Listagem.
- Convites.
- Criação de convites.
- Desativação.
- Reativação.
- Visualização de estado.
- Histórico de ações.

O primeiro administrador será criado pelo processo de bootstrap.

O frontend deverá respeitar a obrigatoriedade de 2FA para ADMIN e impedir o acesso ao painel antes da configuração inicial.

Todas as chamadas sob `/admin/**` são destinadas a usuário autenticado com perfil ADMIN e sessão com 2FA concluído. A interface não substitui a autorização do backend. A consulta `GET /admin/loans` é registrada pelo backend na auditoria e não solicita reautenticação contextual.

## 8.8. Auditoria

- Listagem de eventos.
- Filtros.
- Pesquisa.
- Detalhes.
- Identificação do ator.
- Data e horário.
- Ação realizada.
- Recurso afetado.
- Resultado.
- Justificativa, quando aplicável.

A interface deverá tratar registros de auditoria como informações de consulta, sem edição ou exclusão comum.

## 8.9. Configurações do sistema

- Visualização das configurações.
- Alteração das configurações permitidas.
- Validação de valores.
- Confirmação de mudanças sensíveis.
- Reautenticação.
- 2FA.
- Justificativa e auditoria quando exigidas.

## 8.10. Outbox e eventos

- Listagem de eventos.
- Filtros por estado.
- Visualização de tentativas.
- Último erro.
- Data da última tentativa.
- Identificação do evento.
- Estados apresentados conforme a API: `PENDING`, `PROCESSING`, `PROCESSED` e `FAILED`.
- `PROCESSED` indica sucesso; `FAILED` é falha terminal elegível a reprocessamento após confirmação de elegibilidade pelo backend.
- `DEAD_LETTER` pode ser exibido como encaminhamento/armazenamento operacional, não como estado do domínio.
- Reprocessamento manual autorizado.
- Justificativa obrigatória.
- Reautenticação e auditoria quando exigidas.

O frontend não deverá apagar histórico de tentativas nem manipular diretamente o estado interno dos eventos.

## 8.11. Privacidade

- Consulta de solicitações relacionadas a dados pessoais.
- Visualização de estado.
- Acompanhamento de solicitações.
- Ações administrativas permitidas.
- Confirmação e auditoria de operações sensíveis.

## 9. Autenticação e segurança

## 9.1. Integração com autenticação

O frontend utilizará os mecanismos de autenticação definidos pelo backend.

- Access Token JWT.
- Refresh Token.
- Rotação de Refresh Token.
- Sessões revogáveis.
- 2FA TOTP.
- Reautenticação contextual.

## 9.2. Armazenamento de tokens

- Access Token mantido somente em memória.
- Proibido armazenar Access Token em `localStorage`.
- Proibido armazenar Access Token em `sessionStorage`.
- Refresh Token armazenado em cookie HttpOnly, Secure e SameSite, conforme configuração do backend.
- Não acessar diretamente o conteúdo do cookie pelo JavaScript.

## 9.3. Renovação de sessão

- Renovação por meio do endpoint de refresh.
- Tratamento de expiração.
- Controle de requisições concorrentes de refresh.
- Atualização do Access Token em memória.
- Logout quando a sessão não puder ser renovada.
- Limpeza do estado de autenticação ao encerrar a sessão.

## 9.4. Login com 2FA

Quando o backend retornar um desafio de autenticação:

1. Armazenar temporariamente o identificador do desafio.
2. Exibir a etapa de validação TOTP.
3. Enviar o código ao endpoint de verificação.
4. Receber os tokens somente após validação.
5. Tratar expiração, uso único e tentativas inválidas.

O frontend não deverá considerar o usuário autenticado antes da conclusão do desafio.

## 9.5. Administradores

- 2FA obrigatório.
- Configuração inicial antes do acesso ao painel.
- Reautenticação nas ações críticas.
- Confirmações adicionais.
- Justificativas obrigatórias quando definidas.
- Tratamento de sessão expirada.

## 9.6. Autorização

- Exibição condicional de menus conforme perfil.
- Controle de navegação por perfil.
- Tratamento de respostas HTTP 401 e 403.
- Validação do contexto de sessão.
- Nunca confiar apenas em verificações frontend.

O backend deverá validar perfil, titularidade, permissões e escopo de cada operação.

## 9.7. Proteções adicionais

- Validação de dados.
- Tratamento seguro de mensagens de erro.
- Prevenção de XSS.
- Proteção CSRF conforme utilização de cookies.
- Política CSP compatível com a aplicação.
- Não expor segredos ou credenciais no frontend.
- Não registrar tokens ou dados sensíveis em logs.
- Não confiar em parâmetros fornecidos pelo navegador.
- Não permitir que alterações no cliente contornem regras do servidor.

## 10. Integração com a API REST

## 10.1. Comunicação

- API REST Spring Boot.
- Prefixo `/api/v1`.
- JSON.
- UTF-8.
- Datas em ISO 8601.
- Identificadores UUID.
- Valores monetários em BRL.

## 10.2. Camada de serviços

A comunicação HTTP deverá ser centralizada em uma camada de serviços.

Responsabilidades:

- Configuração do Axios.
- Base URL por ambiente.
- Interceptors.
- Envio de credenciais quando necessário.
- Envio do Access Token.
- Tratamento de erros.
- Renovação de sessão.
- Padronização de respostas.
- Controle de idempotência para operações aplicáveis.

Os componentes visuais não deverão concentrar chamadas HTTP diretamente.

## 10.3. Gerenciamento de estado remoto

TanStack Query será utilizado para:

- Consultas.
- Cache.
- Atualização de dados.
- Invalidação de consultas.
- Paginação.
- Estados de carregamento.
- Estados de erro.
- Sincronização com a API.

O cache não deverá ser tratado como fonte oficial de dados financeiros.

## 10.4. Estado local

O estado local será utilizado para:

- Abertura de modais.
- Filtros temporários.
- Abas selecionadas.
- Dados de formulários.
- Estados visuais.
- Etapas de fluxos.

Evitar duplicar no estado global informações já gerenciadas pelo TanStack Query.

## 10.5. Erros

A interface deverá tratar os erros da API de forma padronizada.

Exemplos:

- 400 — requisição inválida.
- 401 — não autenticado.
- 403 — sem permissão.
- 404 — recurso não encontrado.
- 409 — conflito de estado ou concorrência.
- 422 — validação de negócio.
- 429 — limite de requisições.
- 500 — erro interno.
- 503 — serviço temporariamente indisponível.

As mensagens exibidas ao usuário deverão ser compreensíveis e não revelar detalhes internos.

## 10.6. Idempotência

Operações financeiras deverão respeitar os mecanismos de idempotência definidos pela API.

O frontend deverá:
- Enviar a chave de idempotência quando exigida.
- Evitar disparos duplicados por múltiplos cliques.
- Exibir estado de processamento.
- Não repetir automaticamente operações financeiras sem controle.
- Tratar respostas de conflito.

## 11. Formulários e validação

## 11.1. Bibliotecas

- React Hook Form.
- Zod.

## 11.2. Padrões

- Validação no cliente para experiência do usuário.
- Validação definitiva no backend.
- Mensagens claras.
- Campos obrigatórios identificados.
- Máscaras apenas quando necessárias.
- Tratamento de erros por campo.
- Prevenção de submissões duplicadas.
- Confirmação de operações sensíveis.

## 11.3. Valores financeiros

- Exibição de moeda no padrão brasileiro.
- Formatação BRL.
- Precisão de duas casas decimais.
- Não utilizar cálculos financeiros com ponto flutuante como fonte de verdade.
- Não confiar em valores calculados pelo navegador.
- Backend responsável pelo cálculo e validação oficial.

## 12. Responsividade

A aplicação deverá adotar abordagem mobile-first.

Suporte a:

- Smartphones.
- Tablets.
- Notebooks.
- Desktops.
- Telas de alta resolução.

Requisitos:

- Layout adaptável.
- Navegação adequada a telas pequenas.
- Menus responsivos.
- Tabelas adaptadas.
- Cards reorganizados.
- Formulários utilizáveis em dispositivos móveis.
- Gráficos responsivos.
- Botões e áreas de interação adequados ao toque.

Nenhuma funcionalidade essencial deverá depender exclusivamente de uma resolução específica.

## 13. Acessibilidade

Seguir boas práticas de acessibilidade, considerando WCAG.

Requisitos:

- Contraste adequado.
- Navegação por teclado.
- Foco visível.
- Labels associados aos campos.
- Textos alternativos quando necessários.
- Semântica HTML.
- Mensagens de erro acessíveis.
- Estados não comunicados somente por cor.
- Compatibilidade com leitores de tela.
- Respeito à preferência por redução de movimento.

## 14. Desempenho e experiência do usuário

- Carregamento eficiente.
- Divisão de código por rotas quando apropriado.
- Lazy loading quando adequado.
- Componentes reutilizáveis.
- Evitar renderizações desnecessárias.
- Consultas com paginação.
- Cache controlado.
- Imagens otimizadas.
- Gráficos com quantidade de dados adequada.
- Skeletons para carregamentos.
- Feedback imediato para ações.
- Tratamento de falhas de conexão.

Não realizar otimizações prematuras que aumentem a complexidade sem benefício demonstrável.

## 15. Estados e feedback visual

Todas as telas que dependem da API deverão contemplar:

- Carregamento.
- Sucesso.
- Erro.
- Estado vazio.
- Estado sem resultados.
- Acesso não autorizado.
- Sessão expirada.
- Operação em andamento.
- Operação concluída.
- Operação rejeitada.
- Operação cancelada.

Operações financeiras deverão apresentar confirmação e resultado de forma clara, sem indicar sucesso antes da confirmação do backend.

## 16. Testes e qualidade

## 16.1. Vitest

Testes unitários para:

- Funções utilitárias.
- Formatadores.
- Validadores.
- Hooks.
- Regras de apresentação.
- Componentes isolados.

## 16.2. React Testing Library

Testes de componentes e interações:

- Formulários.
- Navegação.
- Modais.
- Botões.
- Mensagens.
- Estados de carregamento.
- Tratamento de erros.
- Acessibilidade básica.

## 16.3. Playwright

Testes ponta a ponta:

- Cadastro.
- Login.
- Login com 2FA.
- Recuperação de senha.
- Navegação do cliente.
- Contas.
- Transferências.
- Pagamentos.
- Empréstimos.
- Cartões.
- Gestão administrativa.
- Estornos.
- Sessões.
- Permissões e bloqueios de acesso.

## 16.4. Segurança frontend

Testar:

- Tentativa de acessar rotas administrativas como CUSTOMER.
- Acesso direto a URLs protegidas.
- Sessões expiradas.
- Tokens inválidos.
- Renovação de sessão.
- Falhas de autorização.
- Manipulação de parâmetros.
- Submissões duplicadas.
- Dados inválidos.
- Tratamento de erros da API.
- Ausência de tokens em armazenamento persistente do navegador.

Os testes frontend não substituem os testes de autorização e segurança do backend.

## 17. Variáveis de ambiente

Utilizar variáveis de ambiente para configurações específicas do ambiente.

Exemplos:

- URL da API.
- Configurações públicas necessárias ao frontend.
- Identificação do ambiente.

Regras:

- Não armazenar segredos no frontend.
- Não incluir credenciais privadas em variáveis expostas ao navegador.
- Disponibilizar `.env.example`.
- Não versionar arquivos `.env` reais.
- Validar configurações necessárias na inicialização.

## 18. Padrões de desenvolvimento

Aplicar:

- Clean Code.
- SOLID quando aplicável.
- DRY.
- KISS.
- YAGNI.
- Componentização.
- Separação de responsabilidades.
- Nomenclatura consistente.
- Código legível.
- Tipagem e validação de dados em tempo de execução por meio dos schemas definidos.
- Tratamento explícito de erros.
- Testes automatizados.
- Documentação das decisões relevantes.

Evitar:

- Componentes excessivamente grandes.
- Lógica de negócio financeira no frontend.
- Duplicação de código.
- Dependências desnecessárias.
- Estados globais sem necessidade.
- Chamadas HTTP espalhadas pelos componentes.
- Valores mágicos.
- Código morto.
- Imports desnecessários.
- Manipulação direta e desnecessária do DOM.
- Armazenamento inseguro de tokens.
- Confiança em validações exclusivamente frontend.

## 19. Limites de responsabilidade

O frontend não será responsável por:

- Calcular saldos oficiais.
- Registrar lançamentos contábeis.
- Validar definitivamente transferências.
- Autorizar operações financeiras.
- Definir elegibilidade de estornos.
- Aprovar empréstimos.
- Calcular condições contratuais oficiais.
- Controlar concorrência financeira.
- Gerenciar estados de negócio de forma independente.
- Determinar permissões definitivas.
- Armazenar segredos de autenticação.
- Integrar-se diretamente ao PostgreSQL.
- Comunicar-se diretamente com provedores financeiros externos.

Essas responsabilidades pertencem ao backend e aos módulos correspondentes.

## 20. Critérios de aceite

A implementação do frontend será considerada adequada quando:

1. As três áreas estiverem organizadas e funcionais.
2. O design system estiver centralizado e reutilizável.
3. A identidade visual dark-only estiver aplicada.
4. As páginas previstas estiverem implementadas conforme o escopo.
5. A navegação estiver organizada por perfil.
6. A integração com a API REST estiver centralizada.
7. O Access Token permanecer somente em memória.
8. O Refresh Token utilizar cookie seguro.
9. Os fluxos de autenticação e 2FA estiverem integrados.
10. As operações financeiras respeitarem os contratos da API.
11. A interface não substituir validações do backend.
12. A aplicação for responsiva.
13. Os requisitos de acessibilidade forem atendidos.
14. Os estados de carregamento, erro e sucesso estiverem tratados.
15. Os testes automatizados estiverem implementados e aprovados.
16. Não existirem segredos expostos no bundle frontend.
17. A aplicação funcionar nos ambientes definidos pelo projeto.
18. A documentação técnica estiver atualizada.

## 21. Diretriz final

O frontend do MyBank deverá oferecer uma experiência premium, consistente, responsiva e segura, mantendo separação rigorosa entre apresentação e regras de negócio.

A implementação deverá seguir a arquitetura definida para o projeto, os contratos REST aprovados, a especificação funcional e os documentos de segurança.

O desenvolvimento será realizado em etapas pequenas e aprovadas individualmente. Nenhuma etapa posterior poderá ser iniciada sem autorização explícita do usuário.

**Fim da especificação frontend.**

## 22. Decisões complementares aprovadas — CPF e Outbox

O frontend não solicita nem exibe CPF em áreas que não precisem dele e não apresenta CPF, ciphertext ou HMAC em logs de interface.

Na área administrativa da Outbox, a interface pode mostrar o estado, as tentativas e o erro operacional autorizado para consulta. O comando de reprocessamento exige justificativa, confirmação adicional e reautenticação segundo os contratos da API. Após `202 Accepted`, a interface acompanha o resultado pela `statusUrl` recebida, sem inferir sucesso final a partir da aceitação. O histórico de tentativas não pode ser apagado nem editado pelo frontend.

A interface oferece o comando somente quando o backend indicar elegibilidade em estado `FAILED`. `PROCESSED` indica processamento concluído com sucesso. `DEAD_LETTER` pode identificar encaminhamento/armazenamento operacional, mas não é estado nem critério autônomo de elegibilidade.
