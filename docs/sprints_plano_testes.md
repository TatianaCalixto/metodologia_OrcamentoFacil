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
| 20 | deps | Manual | Dependabot dispara PR para vuln injetada propositalmente |  | Pendente |  | Follow-up: teste manual depende do humano ligar "Dependabot security updates" nas Settings (UI) e observar o 1o PR de vuln. Repos publicos ja tem alerts on por padrao; varredura de version update do dependabot.yml ativa via push. Mantido Pendente com justificativa (padrao S17/S19). Ver DEC-004. UPDATE 2026-05-29: Dependabot empiricamente ativo (PRs #1-3 abertos). O literal "PR para vuln injetada" ainda depende do toggle security-updates (DEC-004). \| Follow-up manual do humano (DEC-004): toggle security-updates + validacao do auto-PR. Nao bloqueia a fase. |
| 20 | ci | Integração | pip-audit quebra CI em vuln High/Critical |  | Concluída | 2026-05-29 | CI real verde na main (9fdb7d7): step Security audit=success em requirements limpo. Break-path validado local: jinja2==2.10+pyyaml==5.1 -> exit 1 (6 High/Critical). Mesmo wrapper roda no CI. |
| 20 | rate-limit | Integração | /imports/csv 429 ao estourar 1/min; reset apos janela |  | Concluída | 2026-05-29 | test_ratelimit_user.test_imports_csv_limite_1_por_min (429 + Retry-After) e test_limite_reseta_apos_janela (reset). Verde local. |
| 20 | rate-limit | Integração | /transactions POST 429 ao estourar 60/min por user |  | Concluída | 2026-05-29 | test_ratelimit_user.test_transactions_post_limite_60_por_min (61a -> 429). Verde local. + accounts/budgets/goals, GET livre e isolamento por usuario cobertos no mesmo arquivo. |
| 20 | headers | Integração | 5 security headers presentes em todas as responses |  | Concluída | 2026-05-29 | test_security_headers: 4 headers base presentes no /health e ate em 404. Verde. |
| 20 | headers | Integração | HSTS so em ENVIRONMENT=production |  | Concluída | 2026-05-29 | test_security_headers: HSTS presente com production=True e ausente caso contrario (mini-app). Verde. |
| 20 | auth | Integração | Refresh com token revogado retorna 401 |  | Concluída | 2026-05-29 | test_auth_logout.test_fluxo_register_login_refresh_logout_refresh_401. Verde. |
| 20 | auth | Integração | Logout revoga refresh atual |  | Concluída | 2026-05-29 | test_auth_logout: logout revoga jti; idempotencia; auth obrigatoria; cleanup. Verde. |
| 20 | auth | Unitário | Politica de senha rejeita fraca / aceita forte |  | Concluída | 2026-05-29 | test_users_schemas: rejeita vazia/curta/so-letras/so-numeros; aceita forte. Verde. |
| 21 | ci-mobile | Manual | Workflow Flutter verde em push; quebra em analyze warning |  | Concluída | 2026-05-29 | CI verde na main (a6a25d0). Analyze-break provado empiricamente: warning asset .env ausente deixou o CI vermelho; verde apos o fix. |
| 21 | ci-mobile | Manual | Gate de cobertura 70% quebra com PR reduzindo cobertura |  | Concluída | 2026-05-29 | Gate de cobertura validado local: lcov 50%->exit1, 80%->exit0; cobertura real 73.0% passa. |
| 21 | ci-mobile | Manual | Build APK debug verde; artefato disponivel |  | Concluída | 2026-05-29 | CI build job verde (6700ef6); artefato app-debug-apk (77.8MB, 7 dias) baixavel. Build validado local tambem. Instalar/abrir em emulador = follow-up manual humano. |
| 21 | logging | Integração | Logs enviados ao agregador quando env vars setadas |  | Concluída | 2026-05-29 | test_logging_shipping: com vars, urlopen e chamado (POST + Bearer + JSON). Verde (mockado). |
| 21 | logging | Integração | Sem env vars, comportamento atual preservado (stdout) |  | Concluída | 2026-05-29 | test_logging_shipping: sem vars, nenhum LogShippingHandler; so StreamHandler stdout. Verde. |
| 21 | /healthz | Integração | /healthz retorna status DB e latencia |  | Concluída | 2026-05-29 | test_healthz: DB ok -> 200, database.status=up, latency_ms numerico. Verde. |
| 21 | /healthz | Integração | /healthz retorna 503 quando DB down |  | Concluída | 2026-05-29 | test_healthz: get_db override (DB down) -> 503, database.status=down. Verde. |
| 21 | /metrics | Integração | /metrics expoe counters Prometheus apos requests |  | Concluída | 2026-05-29 | test_metrics: /metrics formato Prometheus; http_requests_total do /health incrementa apos 3 requests. Verde. |
| 22 | deploy-db | Smoke | alembic upgrade head contra DB gerenciado |  | Pendente |  | FASE 3 — adicionado em 2026-05-24 \| S22 deferida (DEC-005/DEC-008/DEC-009): depende de deploy real; reabre com go humano. |
| 22 | deploy-db | Smoke | register + login contra DATABASE_URL remoto |  | Pendente |  | FASE 3 — adicionado em 2026-05-24 \| S22 deferida (DEC-005/DEC-008/DEC-009): depende de deploy real; reabre com go humano. |
| 22 | deploy-api | Smoke | smoke_prod.py contra URL publica verde |  | Pendente |  | FASE 3 — adicionado em 2026-05-24 \| S22 deferida (DEC-005/DEC-008/DEC-009): depende de deploy real; reabre com go humano. |
| 22 | deploy-api | Manual | Auto-deploy dispara em commit para main |  | Pendente |  | FASE 3 — adicionado em 2026-05-24 \| S22 deferida (DEC-005/DEC-008/DEC-009): depende de deploy real; reabre com go humano. |
| 22 | deploy-panel | Manual | Painel publico carrega e loga contra prod |  | Pendente |  | FASE 3 — adicionado em 2026-05-24 \| S22 deferida (DEC-005/DEC-008/DEC-009): depende de deploy real; reabre com go humano. |
| 22 | deploy-mobile | Manual | APK release instala e loga contra prod |  | Pendente |  | FASE 3 — adicionado em 2026-05-24 \| S22 deferida (DEC-005/DEC-008/DEC-009): depende de deploy real; reabre com go humano. |
| 22 | backup | Manual | Restore do ultimo backup em DB de teste local |  | Pendente |  | FASE 3 — adicionado em 2026-05-24 \| S22 deferida (DEC-005/DEC-008/DEC-009): depende de deploy real; reabre com go humano. |
| 23 | /v1 | Integração | Todos endpoints servem em /v1; redirects funcionam |  | Pendente |  | FASE 3 — adicionado em 2026-05-24 \| Sprint 23 opcional (backlog), nao executada nesta fase. |
| 23 | client-dart | Manual | Cliente Dart gerado diff-clean apos regeneracao |  | Pendente |  | FASE 3 — adicionado em 2026-05-24 \| Sprint 23 opcional (backlog), nao executada nesta fase. |
| 23 | e2e | E2E | Playwright: login -> criar conta -> criar transacao -> ver relatorio |  | Pendente |  | FASE 3 — adicionado em 2026-05-24 \| Sprint 23 opcional (backlog), nao executada nesta fase. |
| 23 | seed | Unitário | seed_demo idempotente (rodar 2x nao duplica) |  | Pendente |  | FASE 3 — adicionado em 2026-05-24 \| Sprint 23 opcional (backlog), nao executada nesta fase. |
| 23 | runbook | Manual | 5 cenarios revistos mentalmente e validados |  | Pendente |  | FASE 3 — adicionado em 2026-05-24 \| Sprint 23 opcional (backlog), nao executada nesta fase. |
| 24 | backend | Regressão | Suíte async verde + test_balance_regression_full_flow verde após migração async | tests/ (suite completa, pytest-asyncio modo auto) + tests/test_balance_regression.py | Concluída | 2026-06-03 | 257 testes verdes; cobertura 95.88%; test_balance_regression_full_flow verde. |
| 24 | backend | Integração | UoW: 2 operações na mesma transação, erro na 2ª -> rollback total | tests/test_uow.py | Concluída | 2026-06-03 | 2 ops no mesmo request: caminho feliz commita ambas; erro na 2a faz rollback total (saldo inalterado). 2 testes verdes. |
| 24 | banco | Migração | Índices novos: upgrade/downgrade reversível; query usa o índice | tests/test_db_indices.py | Concluída | 2026-06-03 | upgrade/downgrade reversivel (sqlite isolado); EXPLAIN usa ix_budgets_user_id_month_year, ix_transactions_user_id_date (sqlite) e ix_transactions_description_trgm GIN (Postgres). 3 testes verdes. |
| 25 | mobile | Unit | Models freezed: fromJson/toJson round-trip | test/data/models_roundtrip_test.dart | Concluída | 2026-06-03 | 8 testes: paridade de campos + fromJson/toJson round-trip dos 9 models (Transaction/Page/Account/Category/AccountFull/CategoryFull/Budget*/Goal). |
| 25 | mobile | Widget | AsyncView nos 3 estados (loading/empty/error) | test/shared/widgets/async_view_test.dart | Concluída | 2026-06-03 | 5 testes widget: LoadingView/EmptyView/ErrorView (3 estados) + onRetry + variante scrollable. Telas usam o componente; trios duplicados eliminados. |
| 25 | mobile | Integração | Cache HTTP: GET hit/miss respeitando TTL | mobile: test/core/network/http_cache_test.dart; backend: tests/test_cache_headers.py | Concluída | 2026-06-03 | Hit dentro do TTL nao refaz HTTP; mutacao invalida (miss). Backend envia Cache-Control: private, max-age=30 em GET 2xx (exceto health/metrics). Smoke manual APK nao executado. |
| 26 | painel | Unit | Services com mock api.py + invalidação de cache pós-mutação |  | Pendente |  |  |
| 26 | painel | Unit | transaction_form em modos create e edit |  | Pendente |  |  |
| 27 | mobile | Widget | i18n renderiza em PT e EN [OPCIONAL] |  | Pendente |  |  |
| 27 | mobile | Integração | Cache local persiste entre restarts (Hive) [OPCIONAL] |  | Pendente |  |  |
| 27 | mobile | Integração | Deep link abre tela específica [OPCIONAL] |  | Pendente |  |  |
