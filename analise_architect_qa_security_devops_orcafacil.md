# OrçaFácil — Análise Architect / QA / Security / DevOps (Fim da Fase 2)

**Data:** 2026-05-24
**Escopo:** revisão técnica após o fechamento da Fase 2 (Sprints 17-19 concluídas — gestão de Contas, painel com cadastro completo, polimento UX/UI).
**Papéis:** Software Architect, QA Engineer, Security Engineer, DevOps Engineer.
**Base:** [blueprint](orcafacil_blueprint_completo.md), [planilha de planejamento](orcafacil_planejamento_sprints.xlsx), [análise PO/UX/UI anterior](analise_po_ux_ui_orcafacil.md), código em `backend_OrcamentoFacil/` e `frontend_OrcamentoFacil/`.

---

## 1. Sumário executivo

A Fase 2 fechou todas as lacunas funcionais identificadas pela análise PO/UX/UI: gestão de contas em todas as superfícies, painel com paridade funcional ao mobile, e polimento UX. Olhando agora pelos papéis técnicos, **o produto está funcional mas não está pronto para produção pública**. Quatro achados críticos:

1. **🔴 Não há deploy ativo nem provisionamento de banco gerenciado.** Três tarefas críticas da Sprint 16 (Supabase, Railway/Render, APK) foram canceladas — registrado em IMP-001/002/003. Sem isso o portfólio existe mas o produto não roda fora do localhost do desenvolvedor.
2. **🔴 Mobile sem CI.** O backend tem GitHub Actions verde a cada push; o repo Flutter não tem workflow. Toda regressão mobile precisa ser detectada localmente — não escala.
3. **🟠 Sem varredura de dependências (CVE).** Nenhum Dependabot, pip-audit ou flutter pub outdated no CI. Vulnerabilidades em libs viram dívida silenciosa.
4. **🟠 Observabilidade zero.** Logging estruturado existe (Sprint 1), mas não há agregação, alerta nem dashboard de saúde. Em produção o sistema fica cego.

Outros achados de severidade média/baixa catalogados como P5-P12.

---

## 2. Estado atual — atualizado pós-Fase 2

### Backend (`backend_OrcamentoFacil/backend/`)
- 10 módulos completos: auth, users, accounts, categories, transactions, budgets, goals, dashboard, imports, core.
- 96,6% de cobertura global (alvo: 80%).
- Dockerfile multi-stage com usuário não-root (Sprint 16 T01).
- `scripts/smoke_prod.py` para smoke contra URL externa.
- CI GitHub Actions (lint + pytest com Postgres service).
- Sem deploy ativo.

### Painel (`backend_OrcamentoFacil/panel/`)
- 5 páginas: Relatórios, Transações (lista+ações), Contas, Nova Transação, Importar CSV.
- Tests: `test_api.py`, `test_accounts.py`, `test_imports_panel.py`, `test_transactions_panel.py`.
- `ui.py` + `api.py` como camadas finas — não há arquitetura mais elaborada (Streamlit puro).

### Mobile (`frontend_OrcamentoFacil/mobile/`)
- 11 features: accounts (novo na Fase 2), auth, budgets, categories, dashboard, goals, home (novo), imports, profile, settings, transactions.
- Widget tests em features-chave.
- **Sem CI.** Não há `.github/workflows/` no repo.

### Planejamento (planilha)
- 20 sprints (0-19), 83 tarefas (80 Concluídas + 3 Canceladas).
- 3 cancelamentos: S16-T02 (Supabase), S16-T03 (Deploy), S16-T04 (APK) — decisão registrada em IMP-001/002/003.
- 2 decisões formais (DEC-001, DEC-002).

---

## 3. Análise — Software Architect

### O que está bem
- **Backend em camadas explícitas**: models → repository → service → router. Separação respeitada.
- **Mobile feature-based**: `lib/features/<feature>/{data,application,presentation}` — escalável.
- **Isolamento por usuário em todos os CRUDs**: queries sempre filtradas por `user_id`, com 404 (não 403) para não vazar existência. Boa prática.
- **Decisão de split em 2 repos** documentada (DEC-001) com alternativas consideradas.

### Problemas
1. **Sem versionamento de API.** Endpoints servidos em `/auth/...`, `/accounts/...` etc. — qualquer breaking change vai quebrar todos os clientes. Padrão esperado: `/v1/auth/...`.
2. **Contratos cliente-servidor não compartilhados.** Schemas Pydantic no backend e classes Dart manuais no mobile — qualquer drift gera bug silencioso. Padrão moderno: gerar cliente Dart a partir do OpenAPI.
3. **Painel sem arquitetura.** `panel/pages/*.py` chamam `api.py` direto. Funciona para o tamanho atual, mas duplica lógica que poderia viver em `services/` compartilhados com o backend.
4. **Mobile depende de URL única do backend.** Sem feature flags, sem fallback offline, sem cache HTTP — fica frágil para uso real.

### Recomendações
- **R1 (média):** Adotar prefixo `/v1/` no FastAPI. Atualizar mobile e painel.
- **R2 (média):** Gerar cliente Dart automaticamente do OpenAPI no CI do mobile.
- **R3 (baixa):** Pasta `panel/services/` que centralize as chamadas + cache leve (`@st.cache_data`).
- **R4 (baixa):** Cache HTTP no mobile com cabeçalhos `ETag`/`If-None-Match`.

---

## 4. Análise — QA Engineer

### O que está bem
- **Cobertura backend 96,6%** (alvo 80%) — significativamente acima.
- **Bateria de regressão de saldo** (`test_balance_regression_full_flow`) — guarda perene contra a regra de negócio mais crítica.
- **Widget tests no mobile** cobrindo features-chave da Fase 2.
- **Painel testado** com mocks da camada `api.py`.
- **CI backend roda lint + testes com Postgres service** a cada push.

### Problemas
1. **Mobile sem CI.** Toda regressão tem que ser pega rodando `flutter test` local. Em projeto profissional isso é trava de portfólio.
2. **Sem E2E ponta-a-ponta atravessando tiers.** Os testes existentes são por camada (backend = httpx contra ASGI; mobile = widget com mock). Falta um teste real `mobile → backend → DB → mobile`.
3. **Sem testes de carga/performance.** O backend nunca foi medido. Endpoints de dashboard (queries agregadas) podem degradar rápido com volume.
4. **Cobertura mobile sem alvo formal.** A planilha exige ≥80% no backend a partir da S10; mobile não tem número-alvo. Difícil dizer se o aumento de telas piorou a relação testes/código.
5. **Sem fixtures de dados realistas.** Para reproduzir bugs ou demonstrar o app, precisa cadastrar tudo à mão. Deveria existir um script seed que popula um cenário "1 mês de uso típico".

### Recomendações
- **R5 (alta):** GitHub Actions no repo Flutter — `flutter analyze` + `flutter test` + coverage.
- **R6 (média):** Suite E2E mínima usando Playwright/Cypress contra o painel + chamadas diretas a API.
- **R7 (média):** Alvo de cobertura mobile ≥70% no CI (mais frouxo que backend, condizente com o peso de UI).
- **R8 (baixa):** Script `scripts/seed_demo.py` que cria usuário, 3 contas, 8 categorias, 60 transações distribuídas em 90 dias.
- **R9 (baixa):** Benchmark básico (locust ou k6) dos endpoints de dashboard.

---

## 5. Análise — Security Engineer

### O que está bem
- **JWT access + refresh** com expiração e algoritmo configurável (Sprints 2 e 10).
- **bcrypt para senhas** com `passlib` pinned (`bcrypt>=4.0,<4.1` para evitar regressão conhecida).
- **Rate limiting em `/auth/{register,login}`** (10/min) via slowapi (Sprint 10).
- **CORS por ambiente** (Sprint 10) — produção restringe.
- **Isolamento por usuário em todos os CRUDs**, testado.

### Problemas
1. **Sem varredura de CVE em dependências.** Sem Dependabot, sem `pip-audit`, sem `flutter pub outdated` no CI. Vulnerabilidades novas em libs viram dívida silenciosa.
2. **Rate limit aplicado só em auth.** `/imports/csv` aceita upload sem throttle — abusivo (CPU/memória) e potencialmente vetor de DoS leve. Endpoints de mutação (`/transactions`, `/accounts`) também ficam expostos a flood.
3. **Sem headers de segurança HTTP.** CSP, HSTS, X-Content-Type-Options, Referrer-Policy. Vai pesar muito quando deploy estiver no ar.
4. **Refresh token sem revogação.** Lista negra/whitelist não existe — token roubado fica válido até expirar. Em conta financeira é dor grande.
5. **Sem auditoria de tentativas suspeitas.** Logs estruturados existem, mas nada agrega "X falhas de login do IP Y em N minutos". Sinal trivial de força bruta passa despercebido.
6. **Sem política de senha forte.** Backend aceita qualquer senha não-vazia. Mínimo seria 8 caracteres + complexidade básica.

### Recomendações
- **R10 (alta):** Habilitar Dependabot nos 2 repos + adicionar `pip-audit` no CI backend e `flutter pub outdated --mode=security` no CI mobile (futuro).
- **R11 (alta):** Estender rate limiting para `/imports/csv` (1/min é razoável) e mutações (`/transactions`, `/accounts`) — limite por usuário, não só por IP.
- **R12 (alta):** Middleware de headers de segurança (FastAPI tem libs prontas).
- **R13 (média):** Tabela `revoked_refresh_tokens` + checagem no endpoint `/auth/refresh`. Logout deve revogar.
- **R14 (média):** Política de senha (mínimo 8 chars, 1 letra, 1 número) no schema Pydantic.
- **R15 (baixa):** Métrica simples "tentativas de login por IP / janela" exposta em endpoint admin protegido.

---

## 6. Análise — DevOps Engineer

### O que está bem
- **Dockerfile multi-stage**, usuário não-root, healthcheck (Sprint 16 T01).
- **docker-compose** para Postgres local — desenvolvimento padronizado.
- **Smoke test script** (`scripts/smoke_prod.py`) pronto para usar quando deploy existir.
- **CI backend** com lint + pytest + Postgres service container.
- **Logging estruturado** com request_id (Sprint 1).

### Problemas
1. **Não há deploy ativo nem banco gerenciado.** Tasks S16-T02/T03/T04 canceladas. O portfólio mostra o método mas o produto não roda fora do localhost — perde força de demonstração.
2. **Mobile sem CI.** Já listado em QA — duplico aqui porque é DevOps responsabilidade típica.
3. **Sem CI/CD contínuo.** Mesmo se deploy existisse, não há automação `merge → deploy`. Toda atualização seria manual.
4. **Sem observabilidade.** Logs locais ou em stdout do container. Sem agregação (Loki, CloudWatch, Datadog), sem alerta, sem dashboard de saúde.
5. **Sem backup automatizado do Postgres.** Em prod, perda de dados = perda total.
6. **Sem runbook.** O que fazer quando o backend cai? Quando o disco enche? Não documentado. Em incidente o dev fica perdido.
7. **Sem secrets management.** `.env` está bem para dev, mas em prod precisa de Doppler / Vault / variáveis do PaaS — não tem caminho documentado.

### Recomendações
- **R16 (alta):** Retomar S16-T02/T03/T04 numa sprint dedicada de deploy.
- **R17 (alta):** GitHub Actions no repo mobile (`flutter test` + `flutter build apk --debug` smoke build).
- **R18 (alta):** Backup automatizado via `pg_dump` agendado (job cron no PaaS ou serviço gerenciado do Supabase).
- **R19 (média):** Logging agregado — Better Stack / Logtail (free tier OK para portfólio) ou logs estruturados no Render/Railway com retenção.
- **R20 (média):** CI/CD: ao merge em `main`, deploy automático para staging.
- **R21 (baixa):** `docs/RUNBOOK.md` com cenários básicos: backend não responde, banco cheio, migration falhou.

---

## 7. Problemas consolidados (priorizados)

| # | Severidade | Problema | Onde | Recomendação |
|---|---|---|---|---|
| P1 | 🔴 Crítica | Sem deploy ativo nem banco gerenciado | Infra | R16 |
| P2 | 🔴 Crítica | Mobile sem CI | Mobile repo | R5, R17 |
| P3 | 🟠 Alta | Sem varredura de CVE em dependências | Backend + Mobile | R10 |
| P4 | 🟠 Alta | Rate limiting só em auth | Backend | R11 |
| P5 | 🟠 Alta | Sem headers de segurança HTTP | Backend | R12 |
| P6 | 🟠 Alta | Observabilidade zero | Backend em prod | R19 |
| P7 | 🟡 Média | Sem E2E ponta-a-ponta | Multi-tier | R6 |
| P8 | 🟡 Média | Refresh token sem revogação | Backend auth | R13 |
| P9 | 🟡 Média | Sem versionamento de API | Backend | R1 |
| P10 | 🟡 Média | Sem backup do Postgres | Infra | R18 |
| P11 | 🟡 Média | Cobertura mobile sem alvo | Mobile | R7 |
| P12 | 🟢 Baixa | Sem fixtures/seed para demo | Backend scripts | R8 |
| P13 | 🟢 Baixa | Sem runbook | Docs | R21 |
| P14 | 🟢 Baixa | Contratos não compartilhados (Dart manual) | Mobile + Backend | R2 |

---

## 8. Sprints propostas — Fase 3

Três sprints para fechar os problemas críticos e altos, com uma quarta opcional. Após aprovação, vão para a planilha como Sprints 20, 21 e 22 (e 23 se aprovada).

### Sprint 20 — Hardening de Segurança e Dependências — fecha P3, P4, P5, P8

- **S20-T01** — Habilitar Dependabot nos 2 repos (configs `.github/dependabot.yml`), grupos: prod / dev / actions.
- **S20-T02** — Adicionar `pip-audit` ao CI backend como step obrigatório (falha vermelha em vuln High/Critical).
- **S20-T03** — Estender rate limiting: `/imports/csv` (1/min/user), `/transactions` POST/PATCH/DELETE (60/min/user), `/accounts` mutação (10/min/user). Limite por usuário autenticado, não só por IP.
- **S20-T04** — Middleware de headers de segurança (CSP, HSTS, X-Content-Type-Options, Referrer-Policy, X-Frame-Options).
- **S20-T05** — Tabela `revoked_refresh_tokens` + checagem em `/auth/refresh` + endpoint `/auth/logout` que revoga.
- **S20-T06** — Política de senha forte (≥8 chars, 1 letra, 1 número) no `UserCreate`.
- **Testes obrigatórios:** integração validando rate limit por usuário; teste de refresh revogado retornando 401; teste de senha fraca rejeitada (422); CI quebra propositalmente em vuln para validar o gate.

### Sprint 21 — Pipeline Mobile e Observabilidade — fecha P2, P6, P11

- **S21-T01** — GitHub Actions no repo `frontend_OrcamentoFacil`: `flutter analyze`, `flutter test --coverage`, gate de cobertura ≥70%.
- **S21-T02** — Job adicional no CI mobile: `flutter build apk --debug` para validar build.
- **S21-T03** — Logging agregado: configurar Better Stack (ou Logtail/Grafana Cloud free tier) e enviar logs estruturados do backend. Variáveis via env.
- **S21-T04** — Endpoint `/healthz` enriquecido (DB ping, versão da aplicação, uptime). Distinto de `/health` (já existente).
- **S21-T05** — Métricas básicas Prometheus-compatible em `/metrics` (latência por endpoint, contagem de requests, erros).
- **Testes obrigatórios:** CI mobile verde rodando workflow novo (validar com PR de teste); teste de integração validando `/healthz` com DB OK e DB down; smoke do `/metrics` exibindo contadores.

### Sprint 22 — Deploy e Backup (retomar S16) — fecha P1, P10

- **S22-T01** — Provisionar Supabase (ou Neon/Render Postgres) + aplicar migrations remotamente via `DATABASE_URL`. Atualizar `.env.example`.
- **S22-T02** — Deploy do backend em Render ou Railway (escolha do usuário); configurar secrets; smoke verde pós-deploy via `scripts/smoke_prod.py`.
- **S22-T03** — Deploy do painel Streamlit em Streamlit Community Cloud ou Render (mesma URL/conta).
- **S22-T04** — `flutter build apk --release` apontando para URL de produção; smoke manual em emulador.
- **S22-T05** — Backup automatizado Postgres: usar o backup nativo do Supabase (gratuito até X) ou job cron com `pg_dump` enviando para S3/B2.
- **S22-T06** — `docs/DEPLOY.md` documentando o setup feito + URL pública + credenciais (em vault, não no doc).
- **Testes obrigatórios:** smoke pós-deploy em prod via script; restore-do-último-backup testado manualmente; healthcheck público responde.

### (Opcional) Sprint 23 — Robustez ponta a ponta — fecha P7, P9, P12, P14

- **S23-T01** — Prefixo `/v1/` no backend; atualizar painel + mobile + smoke_prod.
- **S23-T02** — Gerar cliente Dart do OpenAPI no CI mobile (openapi-generator).
- **S23-T03** — Suite E2E mínima cruzando tiers (Playwright contra painel + chamadas reais à API).
- **S23-T04** — `scripts/seed_demo.py` com cenário "1 mês típico" (1 user, 3 contas, 8 categorias, 60 transações distribuídas).
- **S23-T05** — `docs/RUNBOOK.md` com 5 cenários de incidente.
- **Testes obrigatórios:** E2E criar transação via painel verifica saldo via API; cliente Dart gerado é diff-clean (sem mudança espúria).

---

## 9. Riscos e considerações de implementação

- **Sprint 22 depende de você ter conta nos provedores escolhidos.** O agente vai precisar das credenciais ou autorização para criar projeto.
- **Mobile CI demora a estabilizar.** Primeiro workflow Flutter no GitHub Actions exige iteração. Reservar tempo.
- **Backup do Postgres tem custo (mínimo).** O free tier do Supabase tem backup automático mas com retenção limitada.
- **Bateria de regressão de saldo continua sendo a guarda principal.** Toda mudança em rate limit, deploy ou refresh token NÃO PODE quebrar `test_balance_regression_full_flow`.
- **Política de auto-commit + push de docs ao fim de fase** (DEC-003 — ver planilha) será aplicada a partir da Sprint 22. O agente da Fase 3 deve seguir essa rotina, com confirmação humana antes do push.

---

## 10. Próximos passos

1. Você valida quais recomendações entram, quais ficam para depois.
2. Após "OK", as sprints aprovadas são anexadas à planilha (preservando o tracking da Fase 1+2).
3. O prompt do agente executor é atualizado para a Fase 3 com a rotina de fim-de-fase incluída.
4. DEC-003 já vem registrada na aba Decisões (decisão sobre auto-commit/push de docs).
5. Análise commitada no portfólio. Push aguarda seu OK.
