# OrçaFácil — Análise Backend / Mobile / Frontend Engineer (Pré-Fase 4)

**Data:** 2026-05-24
**Escopo:** revisão técnica focada em **qualidade de código, padrões de implementação e produtividade do desenvolvedor**, pelos papéis de **engenheiros de implementação**. Diferente da análise técnica anterior (Architect/QA/Security/DevOps, que olhou infra e governance), aqui olhamos o **código que o engenheiro escreve no dia a dia**.
**Papéis:** Backend Engineer (Python/FastAPI), Mobile Engineer (Flutter), Frontend/Web Engineer (Streamlit).
**Base:** [blueprint](orcafacil_blueprint_completo.md), [planilha](orcafacil_planejamento_sprints.xlsx), análises anteriores ([PO/UX/UI](analise_po_ux_ui_orcafacil.md) e [Architect/QA/Security/DevOps](analise_architect_qa_security_devops_orcafacil.md)), código em `backend_OrcamentoFacil/` e `frontend_OrcamentoFacil/`.

> ⚠️ A Fase 3 (hardening técnico) está planejada mas ainda não executada. Esta análise olha o estado **atual** e identifica problemas que **persistirão mesmo após a Fase 3** — ou seja, problemas de qualidade de código que a Fase 3 (focada em segurança/infra/observabilidade) não cobre.

---

## 1. Sumário executivo

O código atual é **funcional, testado e bem estruturado em camadas**, mas tem três classes de débito técnico que vão crescer rápido se não tratadas:

1. **🟠 Backend síncrono.** Toda a stack usa `SQLAlchemy` sync e `def` em vez de `async def`. Funciona, mas quando o deploy real chegar (Fase 3) o throughput por worker fica limitado e a latência sofre em endpoints que poderiam ser concorrentes (dashboard, imports).
2. **🟠 Boilerplate manual no mobile.** Estados, modelos e `copyWith` escritos à mão em todo lugar. Cada novo campo vira 4 locais para editar. Aumenta a chance de bug em `copyWith` e atrasa qualquer feature nova.
3. **🟠 Painel Streamlit com duplicação inter-páginas.** Cada `pages/*.py` recarrega contas/categorias/filtros do zero. Funciona, mas com 5 páginas já dá pra ver o padrão — com 10 vai virar pesadelo.

Achados adicionais de severidade média/baixa catalogados como P4-P11.

A Fase 4 (proposta) é dedicada a **qualidade de código sustentável** — 3 sprints obrigatórias + 1 opcional.

---

## 2. Estado atual — o que o código revela

### Backend (`backend_OrcamentoFacil/backend/app/`)
- 10 módulos limpos em camadas (`models → repository → service → router → schemas`).
- Sync SQLAlchemy 2.x em todo o código (`Session`, `db.commit()`).
- Service faz `db.commit()` direto (`TransactionService.create_for_user` em [transactions/service.py:95](backend_OrcamentoFacil/backend/app/transactions/service.py)).
- Schemas Pydantic v2, mas com duplicação de campos entre `Create/Update/Read`.
- Decimal usado corretamente para amount.
- Cobertura 96,6% (excelente).

### Mobile (`frontend_OrcamentoFacil/mobile/lib/`)
- Riverpod com `Notifier<State>` consistente.
- Models como classes Dart imutáveis com `copyWith` **escrito à mão** (visto em `TransactionsState`, `Transaction`, `Account`, etc.).
- Sem `build_runner`, sem `freezed`, sem `json_serializable`.
- Estados loading/empty/error implementados, mas duplicados em cada tela.
- Strings hardcoded em PT-BR (`'Transações'`, `'Salvar'`, etc.) — sem i18n.
- Sem cache HTTP no `Dio`; sem persistência local (Hive/drift).
- Sem app icon nem splash custom (Flutter defaults).

### Painel (`backend_OrcamentoFacil/panel/`)
- 5 páginas + `app.py` + `api.py` (wrapper HTTP) + `ui.py` (2 helpers: `render_header`, `filter_categories_by_type`).
- Cada `pages/*.py` chama `api.list_accounts(token)` e `api.list_categories(token)` no carregamento. Sem cache central.
- `@st.cache_data` usado em `2_Transacoes.py` (cache de listagem), mas **não usado** em chamadas auxiliares (contas, categorias).
- Formulário de transação duplicado entre `2_Transacoes.py` (edição) e `4_Nova_Transacao.py` (criação) — lógica de filtragem de categoria por tipo é a única coisa compartilhada (`ui.filter_categories_by_type`).

### Cobertura analítica das fases anteriores
| Fase | Foco | Status |
|---|---|---|
| Fase 1 | MVP funcional | ✅ |
| Fase 2 | UX/UI / lacunas funcionais | ✅ |
| Fase 3 | Hardening técnico (segurança, CI mobile, observabilidade, deploy) | 📋 Planejada |
| Fase 4 (esta análise) | Qualidade de código sustentável | 📋 Proposta |

---

## 3. Análise — Backend Engineer

### O que está bem
- **Separação em camadas explícita e respeitada** (models → repo → service → router → schemas).
- **Regra crítica de saldo encapsulada no service** com snapshot do estado antigo + reaplicação ([transactions/service.py:101-140](backend_OrcamentoFacil/backend/app/transactions/service.py)). Trabalho elegante.
- **Ownership como exception específica** (`OwnershipError`) mapeada para 404 no router — não vaza existência de recurso de outro usuário.
- **Decimal correto** para valores monetários (não float).
- **Migrations enxutas** com Alembic, sem hacks.
- **Cobertura 96,6%** — bem acima do alvo de 80%.

### Problemas
1. **Sync SQLAlchemy em toda a stack.** Cada `db.commit()` bloqueia o worker FastAPI inteiro. Para o app local não dói; em produção (Fase 3) com 10 usuários simultâneos no dashboard, latência sobe. Migração para `AsyncSession` + `async def` nos endpoints destrava concorrência real.
2. **`db.commit()` dentro do service.** Quebra o padrão Unit-of-Work: o service decide quando commitar, em vez do request handler na borda. Implicações: (a) difícil compor 2 operações de service em uma única transação; (b) testes precisam mockar mais; (c) se aparecer fila/background job, o service precisará ser refatorado.
3. **Schemas duplicam campos.** `TransactionCreate`, `TransactionUpdate`, `TransactionRead` repetem `amount`, `date`, `description`, etc. Quando entrar novo campo (ex.: `tags`), precisa lembrar de 3 lugares. Padrão FastAPI moderno: `TransactionBase` com campos comuns + classes que herdam.
4. **Sem indexes derivados das queries reais.** Endpoints filtram por `(user_id, date)`, `(account_id, date)`, `(user_id, category_id, date)`, e fazem `description ILIKE`. As migrations criam só os índices óbvios (PK, FK). Em produção com volume real, performance de `/transactions?date_from=...&search=...` vai degradar.
5. **Mensagens de erro misturadas pt/en.** `"account nao encontrada"` em PT (sem acento) no service; `"Email already registered"` em EN no auth (provável). Inconsistência em log e na resposta de erro pro mobile.
6. **Sem paginação consistente.** `/transactions` tem paginação completa; `/budgets` e `/goals` retornam tudo. Em listas que crescem (orçamentos históricos), vai dar OOM eventualmente.

### Recomendações
- **R1 (alta):** Migrar para SQLAlchemy async (`AsyncSession`) e `async def` nos endpoints. Não dá pra fazer parcial — é tudo ou nada por dependência (`get_db`).
- **R2 (alta):** Mover `db.commit()` para a borda do request via dependency. Service só agrupa lógica; commit é decisão do router.
- **R3 (média):** Criar `TransactionBase`, `AccountBase`, etc. com campos comuns; schemas herdam.
- **R4 (média):** Migration nova criando índices: `(user_id, date)` em transactions; `gin (description gin_trgm_ops)` para busca textual; `(user_id, month, year)` em budgets.
- **R5 (média):** Padronizar todas as mensagens em PT-BR com acento; documentar como convenção em `docs/PADROES.md`.
- **R6 (baixa):** Paginação opcional em `/budgets` e `/goals` (com defaults amplos).

---

## 4. Análise — Mobile Engineer

### O que está bem
- **Riverpod Notifier pattern** aplicado consistentemente em todas as features.
- **Estados imutáveis com `copyWith`** — desde que escritos corretamente.
- **Separação data / application / presentation** clara em cada feature.
- **Camada de rede com interceptors corretos** (auth + unauthorized → logout).
- **Widget tests cobrindo features-chave** com mocks do Dio.
- **Token em storage seguro** (`flutter_secure_storage`).

### Problemas
1. **Boilerplate manual em todo lugar.** Cada `State` tem `copyWith` escrito à mão; cada model tem `==`, `hashCode`, `fromJson`, `toJson` à mão. Em [`transactions_controller.dart`](frontend_OrcamentoFacil/mobile/lib/features/transactions/application/transactions_controller.dart) o `copyWith` tem 12 campos — qualquer campo novo exige editar declaração, `copyWith`, e provavelmente `==`/`hashCode`. **Custo silencioso: bugs em `copyWith` que esquece campos.**
2. **Sem cache HTTP nem persistência local.** Conexão cai = tela em branco. Para um app financeiro pessoal isso é particularmente dolorido (usuário no mercado, sem rede, querendo lançar despesa).
3. **Estados loading/empty/error duplicados.** Cada feature reescreve `_LoadingList`, `_EmptyList`, `_ErrorList`. Vimos em [`transactions_list_screen.dart`](frontend_OrcamentoFacil/mobile/lib/features/transactions/presentation/transactions_list_screen.dart) — provavelmente outras 6+ telas têm a mesma trinca duplicada.
4. **Sem i18n preparado.** Strings em PT-BR hardcoded no widget. Mesmo que o app fique só em PT, ter `AppLocalizations.of(context).save` em vez de `'Salvar'` evita refator caro depois e facilita acessibilidade (screen readers descobrem strings via API).
5. **Sem deep linking nem app links.** Notificação push ou compartilhamento "ver transação X" não consegue abrir a tela específica — só o Dashboard.
6. **Sem app icon nem splash custom.** O app aparece com ícone azul padrão do Flutter na home do Android. Empurra a percepção de "protótipo, não produto".
7. **Sem build_runner / code gen.** Toda a duplicação acima resolve com `freezed` + `json_serializable` + `build_runner`. Setup leva 1 sprint; ganho de produtividade é permanente.

### Recomendações
- **R7 (alta):** Setup `freezed` + `json_serializable` + `build_runner`. Refatorar estados e modelos progressivamente (estado por sprint, não tudo de uma vez).
- **R8 (alta):** Componente compartilhado `AsyncValueView<T>` (ou trio `LoadingView`/`ErrorView`/`EmptyView`) em `lib/shared/widgets/`.
- **R9 (média):** i18n base com `flutter_localizations` + arb files (mesmo só PT-BR no início).
- **R10 (média):** App icon + splash customizados (`flutter_launcher_icons` + `flutter_native_splash`).
- **R11 (média):** Cache HTTP no `Dio` via `dio_cache_interceptor` para GETs com `Cache-Control`.
- **R12 (baixa):** Deep linking via `go_router` + `androidmanifest.xml` (`<intent-filter>`).
- **R13 (baixa, opcional/Fase 5):** Persistência local com Hive ou Drift para offline-first verdadeiro.

---

## 5. Análise — Frontend/Web Engineer (Streamlit)

### O que está bem
- **5 páginas funcionais** cobrindo todas as ações do app na superfície desktop.
- **Login + session_state** funcional.
- **Wrapper `api.py`** centraliza chamadas HTTP — boa decisão.
- **`ui.py`** com 2 helpers reutilizáveis — embrião correto de camada compartilhada.
- **Cache estratégico em `2_Transacoes.py`** (`@st.cache_data` para listagem).

### Problemas
1. **Duplicação de carregamento entre páginas.** Cada `pages/*.py` chama `api.list_accounts(token)` e `api.list_categories(token)` no `script run`. Sem cache compartilhado, cada navegação refaz HTTP. Em produção (Fase 3) com latência real, isso vira UX ruim.
2. **`@st.cache_data` usado pontualmente, não como padrão.** Dados auxiliares (contas, categorias) deveriam ter cache global — só invalidam quando o usuário cria/edita/exclui.
3. **Formulários duplicados.** O form de transação aparece em `4_Nova_Transacao.py` (criação) e na edição (futura) dentro de `2_Transacoes.py`. Campos e validações similares. Componente compartilhado em `panel/components/transaction_form.py` resolveria.
4. **Filtros não persistem entre navegações.** Sair da página Transações e voltar = filtros zerados. `st.session_state` poderia guardar.
5. **Layout não responsivo para mobile-web.** `st.columns(3)` quebra em telas estreitas. Streamlit tem limitações aqui, mas dá pra mitigar com `st.container` + colunas condicionais.
6. **Sem testes do `ui.py`.** Apenas `filter_categories_by_type` testável; faltaria.

### Recomendações
- **R14 (alta):** Criar `panel/services/` com `accounts_service.py`, `categories_service.py`, `transactions_service.py` que centralizam chamadas + cache. Páginas consomem services em vez de `api.py` direto.
- **R15 (alta):** Disciplina de `@st.cache_data` em todas as listas auxiliares com `ttl` razoável (60s para contas/categorias, que mudam pouco).
- **R16 (média):** `panel/components/transaction_form.py` com modo `create | edit`. Reaproveitado pelas páginas 2 e 4.
- **R17 (média):** Persistência de filtros em `st.session_state['transactions_filters']` com fallback ao default.
- **R18 (baixa):** Testes unitários do novo `ui.py` enriquecido.
- **R19 (baixa):** Componente `filters_bar` parametrizável.

---

## 6. Problemas consolidados (priorizados)

| # | Sev | Problema | Onde | Recomendação |
|---|---|---|---|---|
| P1 | 🟠 Alta | Backend sync limita throughput em prod | Backend (todos módulos) | R1 |
| P2 | 🟠 Alta | Boilerplate manual em modelos/estados mobile | Mobile (todas features) | R7 |
| P3 | 🟠 Alta | Duplicação de carregamento entre páginas do painel | Painel (todas pages) | R14, R15 |
| P4 | 🟠 Alta | `db.commit()` dentro do service quebra UoW | Backend services | R2 |
| P5 | 🟡 Média | Schemas Pydantic duplicam campos | Backend schemas | R3 |
| P6 | 🟡 Média | Sem indexes derivados de queries reais | Backend DB | R4 |
| P7 | 🟡 Média | Estados loading/empty/error duplicados | Mobile telas | R8 |
| P8 | 🟡 Média | Sem i18n preparado | Mobile | R9 |
| P9 | 🟡 Média | Sem cache HTTP no Dio | Mobile | R11 |
| P10 | 🟡 Média | Formulários duplicados no painel | Painel | R16 |
| P11 | 🟢 Baixa | Mensagens de erro mistura pt/en | Backend | R5 |
| P12 | 🟢 Baixa | Sem app icon nem splash custom | Mobile | R10 |
| P13 | 🟢 Baixa | Filtros não persistem entre navegações | Painel | R17 |
| P14 | 🟢 Baixa | Sem paginação em /budgets, /goals | Backend | R6 |

---

## 7. Sprints propostas — Fase 4

Três sprints obrigatórias + uma opcional. Padrão idêntico às fases anteriores. Após aprovação, viram Sprints 24, 25, 26 (e 27 se aprovada) na planilha.

### Sprint 24 — Refatoração e Async no Backend — fecha P1, P4, P5, P6

- **S24-T01** — Migrar para `AsyncSession` (SQLAlchemy async). `get_db()` retorna AsyncSession; todos services e repositories viram `async def`; routers viram `async def`.
- **S24-T02** — Mover `db.commit()` do service para dependency na borda. Padrão UoW: service só agrupa lógica, dependency commita ou rollback no fim do request.
- **S24-T03** — Criar `*Base` schemas para cada entidade (Transaction, Account, Category, Budget, Goal). Refatorar Create/Update/Read para herdar.
- **S24-T04** — Migration adicionando indexes derivados de queries reais: `(user_id, date)` em transactions, `gin (description gin_trgm_ops)` para search, `(user_id, month, year)` em budgets.
- **S24-T05** — Padronização de mensagens de erro em PT-BR com acento. `docs/PADROES.md` com convenção (incluir formato de erro padronizado).
- **S24-T06** — Paginação opcional em `/budgets` e `/goals` (page_size default 50; sem filtros = mais antigos primeiro).
- **Testes obrigatórios:** suite inteira de testes backend reescrita para `pytest-asyncio` no modo async (já está em `auto`); `test_balance_regression_full_flow` continua verde; teste novo de UoW (2 operações em mesma transação → rollback em erro); benchmark básico antes/depois com locust (esperado: aumento de throughput em endpoints concorrentes).

### Sprint 25 — Code Generation e Qualidade Mobile — fecha P2, P7, P9, P12

- **S25-T01** — Setup `freezed` + `json_serializable` + `build_runner` + `freezed_annotation` no `pubspec.yaml`. `dart run build_runner build --delete-conflicting-outputs` no CI mobile (na S21 da Fase 3 deve estar verde).
- **S25-T02** — Refatorar `Transaction`, `Account`, `Category`, `Budget`, `Goal` para freezed. JsonSerializable para `fromJson/toJson`. Verificar testes continuam verdes.
- **S25-T03** — Refatorar estados de cada feature (`TransactionsState`, `AccountsState`, etc.) para freezed.
- **S25-T04** — Criar `lib/shared/widgets/async_view.dart` com `LoadingView`, `EmptyView`, `ErrorView` parametrizáveis. Substituir os componentes locais nas telas existentes.
- **S25-T05** — Configurar `dio_cache_interceptor` no `Dio` para GETs com `Cache-Control: max-age=N`. Backend (S24-T04) precisa setar os headers — coordenar.
- **S25-T06** — App icon + splash customizados via `flutter_launcher_icons` + `flutter_native_splash`. Logo simples (texto OK no MVP).
- **Testes obrigatórios:** widget tests preservados; novo widget test do `AsyncView` em 3 estados; CI mobile (S21) deve seguir verde; build APK debug com cache HTTP funcional (smoke manual).

### Sprint 26 — DRY no Painel e Componentes Reutilizáveis — fecha P3, P10, P13

- **S26-T01** — Criar `panel/services/` com `accounts_service.py`, `categories_service.py`, `transactions_service.py`. Cada service expõe `list_*`, `create_*`, `update_*`, `delete_*` com cache (`@st.cache_data` com `ttl=60` para listas; invalidação manual em mutações).
- **S26-T02** — Refatorar `pages/*.py` para consumir services em vez de `api.py` direto.
- **S26-T03** — Criar `panel/components/transaction_form.py` com modo `create | edit`. Reaproveitado pelas pages 2 (edição) e 4 (criação).
- **S26-T04** — Persistência de filtros em `st.session_state['transactions_filters']` na página de Transações.
- **S26-T05** — Componente `panel/components/filters_bar.py` parametrizável (lista de filtros configurável).
- **S26-T06** — Testes unitários novos em `panel/tests/test_services.py` e `panel/tests/test_components.py`.
- **Testes obrigatórios:** testes de services com mock da `api.py`; teste de cache invalidation após mutação; teste do `transaction_form` em ambos os modos; suite painel continua verde.

### (Opcional) Sprint 27 — Offline-first e i18n no Mobile — fecha P8

- **S27-T01** — i18n base com `flutter_localizations` + arb files (PT-BR + EN, mesmo que EN fique 90% igual no início).
- **S27-T02** — Substituir strings hardcoded por `AppLocalizations.of(context).<chave>` nas 11 features (refator incremental: 2 features por commit).
- **S27-T03** — Cache local com Hive: cachear última lista de transações, contas, categorias. Read-through pattern.
- **S27-T04** — Deep linking via go_router + manifest Android.
- **Testes obrigatórios:** widget test de i18n (renderiza em PT e EN); teste de cache local persistindo entre restarts; teste de deep link abrindo tela específica.

---

## 8. Riscos e considerações de implementação

- **Sprint 24 (async migration) é a mais arriscada.** Migration de sync para async em SQLAlchemy não é "trocar palavras" — algumas APIs mudam (ex.: `db.get()` vira `await db.get()`, lazy loading muda comportamento, sessions são `AsyncSession`). Reservar tempo. **Bateria de regressão de saldo (`test_balance_regression_full_flow`) precisa continuar verde — se quebrar, parar e investigar.**
- **Sprint 25 depende de CI mobile estar verde (Fase 3 Sprint 21).** Sem CI mobile não dá pra rodar `build_runner` em PR.
- **Sprint 26 quase não toca o backend.** Bem isolada — pode ser executada em paralelo às outras se houver mais de um agente.
- **Sprint 27 (i18n + offline) é grande.** Por isso opcional/backlog. Se for executar, dividir em 2 sub-sprints menores.
- **Esta análise foi feita ANTES da Fase 3 ser executada.** Quando a Fase 3 terminar, vale uma releitura para confirmar que os problemas P1-P14 continuam válidos e que nada novo surgiu no caminho.
- **Política de auto-commit de docs (DEC-003)** aplicada a partir da Fase 3 continua valendo na Fase 4 — o agente da Fase 4 segue a mesma rotina de fim-de-fase.

---

## 9. Próximos passos

1. Você valida quais recomendações entram, quais ficam para depois.
2. Após "OK", as sprints aprovadas são anexadas à planilha como Sprints 24, 25, 26 (e 27 se aprovada) — preservando todo o tracking das Fases 1, 2 e 3.
3. PROMPT_INICIAL_CLAUDE_CODE.md atualizado para Fase 4 (após Fase 3 estar concluída de fato — não faz sentido abrir a Fase 4 antes que a 3 termine).
4. DEC-004 registrada na aba Decisões justificando a abertura da Fase 4.
5. Análise commitada no portfólio. Push aguarda OK humano (DEC-003).
