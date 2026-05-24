# Plano de Testes

> Renderizado a partir de `orcafacil_planejamento_sprints.xlsx` (aba **Plano de Testes**).

| Sprint Alvo | Módulo | Tipo de Teste | Descrição | Arquivo de Teste | Status | Última Execução | Observações |
|---|---|---|---|---|---|---|---|
| 1 | core | Integração | GET /health responde 200 |  | Pendente |  |  |
| 1 | core | Unitário | Settings carrega .env de teste |  | Pendente |  |  |
| 1 | database | Integração | Migration inicial up/down |  | Pendente |  |  |
| 1 | errors | Integração | ExceptionHandler retorna payload padronizado |  | Pendente |  |  |
| 2 | auth | Unitário | hash/verify de senha |  | Pendente |  |  |
| 2 | auth | Unitário | JWT issue/decode/expirado/chave errada |  | Pendente |  |  |
| 2 | auth | Integração | Register feliz / email duplicado / inválido |  | Pendente |  |  |
| 2 | auth | Integração | Login feliz / senha errada / email inexistente |  | Pendente |  |  |
| 2 | auth | Integração | /users/me sem token / token inválido / expirado / usuário deletado |  | Pendente |  |  |
| 3 | accounts | Integração | CRUD completo de Account |  | Pendente |  |  |
| 3 | accounts | Integração | Isolamento de Account entre usuários |  | Pendente |  |  |
| 4 | categories | Integração | Seed das 8 categorias padrão no registro |  | Pendente |  |  |
| 4 | categories | Integração | CRUD de Category com isolamento |  | Pendente |  |  |
| 5 | transactions | Unitário | Receita aumenta saldo / Despesa diminui |  | Pendente |  |  |
| 5 | transactions | Unitário | Edição ajusta delta corretamente |  | Pendente |  |  |
| 5 | transactions | Unitário | Mudança de tipo (receita<->despesa) ajusta saldo |  | Pendente |  |  |
| 5 | transactions | Unitário | Deleção reverte saldo |  | Pendente |  |  |
| 5 | transactions | Integração | Filtros, paginação, busca textual |  | Pendente |  |  |
| 5 | transactions | Regressão | Cenário longo end-to-end de saldo (test_balance_regression_full_flow) |  | Pendente |  |  |
| 6 | budgets | Unitário | Cálculo de percent_used e status (ok/warning/critical) |  | Pendente |  |  |
| 6 | budgets | Integração | Comparativo mensal e isolamento |  | Pendente |  |  |
| 7 | goals | Integração | Transição automática para concluída e reversão |  | Pendente |  |  |
| 8 | dashboard | Integração | monthly-summary vazio e populado |  | Pendente |  |  |
| 8 | dashboard | Integração | category-breakdown e cashflow |  | Pendente |  |  |
| 9 | imports | Unitário | Parser CSV com formatos válidos e inválidos |  | Pendente |  |  |
| 9 | imports | Unitário | Regras de categorização (UBER/IFOOD/NETFLIX/fallback) |  | Pendente |  |  |
| 9 | imports | Integração | Import feliz atualiza saldo; erro causa rollback total |  | Pendente |  |  |
| 10 | auth | Integração | Rate limiting em /auth/login retorna 429 |  | Pendente |  |  |
| 10 | auth | Integração | Refresh token feliz / inválido / expirado |  | Pendente |  |  |
| 10 | global | Cobertura | pytest --cov >= 80% global |  | Pendente |  |  |
| 11 | mobile | Widget | Splash navega para Login ou Dashboard conforme token |  | Pendente |  |  |
| 11 | mobile | Widget | Login com mock do Dio (sucesso e erro) |  | Pendente |  |  |
| 11 | mobile | Widget | Cadastro com mock do Dio |  | Pendente |  |  |
| 12 | mobile | Widget | Dashboard nos estados vazio/carregando/erro |  | Pendente |  |  |
| 12 | mobile | Widget | Lista de transações com filtros e scroll |  | Pendente |  |  |
| 12 | mobile | Widget | Formulários de criar/editar transação |  | Pendente |  |  |
| 13 | mobile | Widget | Orçamento exibe corretamente nos 3 status |  | Pendente |  |  |
| 13 | mobile | Widget | Metas em progresso parcial e total |  | Pendente |  |  |
| 13 | mobile | Widget | Categorias CRUD |  | Pendente |  |  |
| 14 | mobile | Widget | Logout limpa token e volta para Login |  | Pendente |  |  |
| 14 | mobile | Widget | Upload CSV exibe sucesso/erro |  | Pendente |  |  |
| 15 | panel | Manual | Login no painel Streamlit |  | Pendente |  |  |
| 15 | panel | Manual | Relatórios e filtros do painel |  | Pendente |  |  |
| 16 | deploy | Smoke | /health em produção |  | Pendente |  |  |
| 16 | deploy | Smoke | Register e login em produção |  | Pendente |  |  |
| 16 | deploy | Manual | APK instala e loga contra produção |  | Pendente |  |  |
| 17 | accounts (mobile) | Widget | Tela Contas nos 3 estados (loading/vazio/com dados) | frontend_OrcamentoFacil/mobile/test/features/accounts/accounts_screen_test.dart | Concluída | 2026-05-24 | 9 testes verdes (loading, vazio, com dados, erro, filtro, confirmar/cancelar delete, FAB, tap card). |
| 17 | accounts (mobile) | Widget | Formulário de conta criar/editar com mock | frontend_OrcamentoFacil/mobile/test/features/accounts/account_form_screen_test.dart | Concluída | 2026-05-24 | 8 testes verdes: criar feliz (BR), aceita ponto decimal, validação nome vazio, validação saldo inválido, erro backend create, editar pré-popula+PATCH, erro backend update, erro GET inicial. |
| 17 | accounts (mobile) | Integração | E2E: criar conta -> lançar transação -> saldo correto |  | Pendente |  | Não implementado como integration test E2E mobile. Cobertura existente: (a) widget tests do form de Contas + Transações via mocks (paths felizes); (b) backend já tem test_balance_regression_full_flow validando o cenário no servidor. Mantido Pendente — fica para uma sprint futura de E2E real (Patrol/Maestro) se houver demanda. |
| 17 | accounts (panel) | Unitário | CRUD de conta no painel com mock da api | backend_OrcamentoFacil/panel/tests/test_accounts.py | Concluída | 2026-05-24 | 6 testes verdes (create payload+erro, update parcial+completo, delete OK+erro). Mocks via unittest.mock.patch em api.requests.*. |
| 17 | regressão | Regressão | test_balance_regression_full_flow continua verde após S17 | backend_OrcamentoFacil/backend/tests/test_balance_regression.py | Concluída | 2026-05-24 | Backend NÃO foi modificado em S17 (T01/T02/T03 = mobile Flutter; T04 = painel Streamlit consome API existente). Bateria trivialmente preservada — nenhuma linha do backend Python alterada nesta sprint. |
| 18 | panel transactions | Unitário | Nova Transação: payload correto + filtro de categorias por tipo | backend_OrcamentoFacil/panel/tests/test_transactions_panel.py | Concluída | 2026-05-24 | create_transaction payload correto + amount como string + filter_categories_by_type unit. |
| 18 | panel transactions | Unitário | Edição via lista do painel (PATCH mock) | backend_OrcamentoFacil/panel/tests/test_transactions_panel.py | Concluída | 2026-05-24 | update_transaction com PATCH parcial (omite None) + erro do backend. |
| 18 | panel transactions | Unitário | Exclusão com confirmação (DELETE mock) | backend_OrcamentoFacil/panel/tests/test_transactions_panel.py | Concluída | 2026-05-24 | delete_transaction URL correta + retorno None + propagação de erro. Confirmação UI testada via lógica de checkbox. |
| 18 | panel imports | Unitário | Upload de CSV no painel (válido / inválido / erros parciais) | backend_OrcamentoFacil/panel/tests/test_imports_panel.py | Concluída | 2026-05-24 | import_csv multipart (files+data) + parser de resumo com erros parciais + erro 400 do backend. |
| 18 | regressão | Smoke | E2E painel: login -> criar conta -> criar transação -> ver no relatório |  | Pendente |  | Smoke E2E manual via Streamlit rodando — não executado por falta de backend up neste momento. Cobertura existente: testes unitários do wrapper api.py cobrem todos os endpoints novos. |
| 18 | regressão | Regressão | test_balance_regression_full_flow continua verde após S18 | backend_OrcamentoFacil/backend/tests/test_balance_regression.py | Concluída | 2026-05-24 | Backend NÃO foi modificado em S18 (todas as 5 tarefas mexem apenas no painel Streamlit). Bateria trivialmente preservada. |
| 19 | mobile dashboard | Widget | FAB no Dashboard navega para /transactions/new | frontend_OrcamentoFacil/mobile/test/features/dashboard/dashboard_screen_test.dart | Concluída | 2026-05-24 | Widget test: FAB presente + clique navega para /transactions/new. |
| 19 | mobile onboarding | Widget | Usuário sem conta vê modal; com conta não vê; Pular avisa | frontend_OrcamentoFacil/mobile/test/features/dashboard/onboarding_test.dart | Concluída | 2026-05-24 | 4 widget tests: sem conta vê, com conta não vê, Pular avisa, Criar navega para /accounts/new. |
| 19 | mobile theme | Golden | 5 telas principais nos modos claro e escuro | frontend_OrcamentoFacil/mobile/test/core/theme_test.dart | Pendente |  | Golden tests baseline NÃO foram criados (frágeis sem CI dedicado). Cobertura existente: theme_test.dart valida que cores semânticas mantêm hex original (anti-regressão lógica) + schemes light/dark instanciam corretamente. Visual manual fica pendente para sessão dedicada. |
| 19 | mobile drawer | Widget | Item ativo do drawer reflete a rota atual | frontend_OrcamentoFacil/mobile/test/features/dashboard/drawer_test.dart | Concluída | 2026-05-24 | 4 widget tests novos no group "AppNavigationDrawer highlight": /transactions → index 1, /budgets → index 4, subrota /transactions/new → index 1, rotas fora retornam null. |
| 19 | mobile transactions | Widget | Snackbar pós-transação exibe novo saldo formatado BRL | frontend_OrcamentoFacil/mobile/test/features/transactions/transaction_form_screen_test.dart | Concluída | 2026-05-24 | 3 widget tests no group "snackbar de saldo": criação BRL, edição BRL, fallback se accounts API falhar. |
| 19 | mobile categories | Widget | Picker de ícone abre, seleciona e persiste corretamente | frontend_OrcamentoFacil/mobile/test/features/categories/ | Concluída | 2026-05-24 | category_form_screen_test.dart: picker abre, seleção muda estado, submit envia string correta. category_visuals_test.dart: mapeamento string→IconData + fallback + cobertura ≥20 ícones. 5 testes verdes no total dos novos. |
