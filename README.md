# OrçaFácil — Caso de uso de _Documentação Viva + Agente Executor_

> Repositório de **metodologia**, não de produto. Como planejar e executar um app fullstack com Claude Code mantendo uma planilha como fonte única de verdade, travando o agente para não tomar decisões fora do escopo e auditando por papéis ao final de cada fase.

O OrçaFácil é um app de controle financeiro pessoal (backend FastAPI, mobile Flutter, painel Streamlit, PostgreSQL). Mas este repositório **não é sobre o app** — é sobre o **método** usado para construí-lo. O app é só o substrato concreto onde o método foi exercitado.

---

## O método em 30 segundos

1. **Blueprint** ([orcafacil_blueprint_completo.md](orcafacil_blueprint_completo.md)) — visão funcional escrita uma vez, evolui pouco.
2. **Planejamento estruturado** — blueprint vira sprints + tarefas + critérios de aceitação + **testes obrigatórios** numa planilha que serve de fonte única de verdade. Versão legível: [docs/sprints_index.md](docs/sprints_index.md).
3. **Prompt do agente executor** ([PROMPT_INICIAL_CLAUDE_CODE.md](PROMPT_INICIAL_CLAUDE_CODE.md)) — outro chat do Claude Code recebe regras inegociáveis: sem teste verde, tarefa não fecha; em dúvida, parar e perguntar; nunca decidir fora da planilha.
4. **Análise por papéis ao fim de cada fase** ([analise_po_ux_ui_orcafacil.md](analise_po_ux_ui_orcafacil.md)) — PO, UX, UI (e outros, conforme a fase) revisam o estado e propõem a próxima fase.
5. **Iteração** — análise vira novas sprints na planilha → novo prompt do executor → ciclo se repete.

---

## Por que vale a pena olhar

- O **planejamento existe e é auditável** — cada tarefa concluída tem critério verificável e testes vinculados.
- O **histórico de decisões existe** — aba `Decisões` da planilha documenta por que cada escolha técnica foi feita ([docs/sprints_decisoes.md](docs/sprints_decisoes.md)).
- A **separação produto vs. método é explícita** — o agente executor não inventa features; quem inventa é o ciclo de análise por papéis.
- A **regressão é uma trava operacional**, não uma intenção — sem teste verde, tarefa não vira "Concluída". Cobertura ≥ 80% obrigatória no backend.
- **Fases existem e são respeitadas** — Sprints 0-16 = Fase 1 (MVP), Sprints 17-19 = Fase 2 (lacunas UX/UI), Sprints 20-23 = Fase 3 (hardening técnico). O agente de cada fase não toca em código das fases anteriores.

---

## Artefatos principais

### Documentos vivos

| Documento | O que é |
|---|---|
| [orcafacil_blueprint_completo.md](orcafacil_blueprint_completo.md) | Blueprint funcional do produto — escrito uma vez |
| [PROMPT_INICIAL_CLAUDE_CODE.md](PROMPT_INICIAL_CLAUDE_CODE.md) | Prompt-modelo do agente executor (com regras inegociáveis) |
| [analise_po_ux_ui_orcafacil.md](analise_po_ux_ui_orcafacil.md) | Análise de fim de Fase 1 pelos papéis Product Owner, UX e UI Designer (motivou a Fase 2) |
| [analise_architect_qa_security_devops_orcafacil.md](analise_architect_qa_security_devops_orcafacil.md) | Análise de fim de Fase 2 pelos papéis Software Architect, QA, Security e DevOps Engineers (motivou a Fase 3) |
| [papeis_ideais_produto_orcafacil.md](papeis_ideais_produto_orcafacil.md) | Catálogo de papéis ideais (PO, UX, UI, Architect, QA, Security, DevOps, etc.) usado como referência para conduzir as análises de fim de fase |
| [orcafacil_planejamento_sprints.xlsx](orcafacil_planejamento_sprints.xlsx) | Planilha de planejamento (fonte única de verdade operacional) |

### Planejamento renderizado (legível no GitHub)

| Arquivo | Aba da planilha |
|---|---|
| [docs/sprints_index.md](docs/sprints_index.md) | Índice |
| [docs/sprints_visao_geral.md](docs/sprints_visao_geral.md) | Visão geral das 24 sprints (Fases 1 a 3) |
| [docs/sprints_backlog.md](docs/sprints_backlog.md) | 105 tarefas com critérios, dependências e testes obrigatórios |
| [docs/sprints_plano_testes.md](docs/sprints_plano_testes.md) | Plano de testes (regressão, integração, unitários, widget, smoke) |
| [docs/sprints_decisoes.md](docs/sprints_decisoes.md) | Log de decisões técnicas |
| [docs/sprints_impedimentos.md](docs/sprints_impedimentos.md) | Log de impedimentos — 3 casos reais em que o agente parou, perguntou e aguardou decisão (prova viva do protocolo "em dúvida, parar") |
| [docs/sprints_instrucoes.md](docs/sprints_instrucoes.md) | Protocolo de uso da planilha pelo agente |

### Scripts

| Script | O que faz |
|---|---|
| [gerar_planilha_sprints.py](gerar_planilha_sprints.py) | Gera a planilha XLSX a partir das definições de sprints/tarefas/testes em Python |
| [adicionar_sprints_17_18_19.py](adicionar_sprints_17_18_19.py) | Anexa as sprints da Fase 2 na planilha existente, preservando o tracking da Fase 1 |

### Código (repositórios irmãos)

- **backend + painel Streamlit**: `backend_OrcamentoFacil/` _(repositório próprio)_
- **mobile Flutter**: `frontend_OrcamentoFacil/` _(repositório próprio)_

A separação em dois repos foi uma decisão consciente registrada em [DEC-001 da aba Decisões](docs/sprints_decisoes.md). Eles existem como _evidência_ de que o método produziu código real, mas o foco deste repositório é a metodologia.

---

## O que o produto faz (resumo)

Controle financeiro pessoal: cadastro de contas e categorias, lançamento de receitas/despesas, orçamentos mensais por categoria com alertas, metas financeiras, dashboard com gráficos, importação de extratos CSV. Backend FastAPI + JWT, app Flutter + Riverpod, painel Streamlit, PostgreSQL. Tudo descrito em detalhe no [blueprint](orcafacil_blueprint_completo.md).

---

## Estrutura do repositório

```
.
├── README.md                                 # você está aqui
├── orcafacil_blueprint_completo.md           # blueprint funcional
├── PROMPT_INICIAL_CLAUDE_CODE.md             # prompt do agente executor
├── analise_po_ux_ui_orcafacil.md             # análise de fim de Fase 1 (PO/UX/UI)
├── analise_architect_qa_security_devops_orcafacil.md  # análise de fim de Fase 2 (Architect/QA/Security/DevOps)
├── papeis_ideais_produto_orcafacil.md        # catálogo de papéis (referência das análises)
├── orcafacil_planejamento_sprints.xlsx       # planilha (fonte única de verdade)
├── gerar_planilha_sprints.py                 # gera a planilha do zero
├── adicionar_sprints_17_18_19.py             # anexa Fase 2 sem destruir Fase 1
├── adicionar_sprints_20_21_22_23.py          # anexa Fase 3 + DEC-003 (auto-commit docs)
├── docs/                                     # planilha renderizada em MD
│   ├── sprints_index.md
│   ├── sprints_visao_geral.md
│   ├── sprints_backlog.md
│   ├── sprints_plano_testes.md
│   ├── sprints_decisoes.md
│   ├── sprints_impedimentos.md
│   └── sprints_instrucoes.md
├── backend_OrcamentoFacil/                   # repo do backend + painel
└── frontend_OrcamentoFacil/                  # repo do mobile
```

---

## Como reproduzir o método em outro projeto

1. **Escreva o blueprint funcional** (1 arquivo MD, 1 a 5 páginas — o que o produto faz, sem decisões de implementação).
2. **Gere a planilha de sprints** a partir do blueprint, com tarefas + critérios + dependências + testes obrigatórios. (Skill `sprint-planning` automatiza isso.)
3. **Gere o prompt do agente executor** apontando para a planilha como fonte única de verdade, com regras inegociáveis.
4. **Abra um chat do Claude Code** com o prompt, deixe ele rodar tarefa por tarefa, marcando status na planilha.
5. **Ao fim de cada fase**, conduza análise pelos papéis que fizerem sentido (PO, UX, UI, Architect, QA, Security, etc.). (Skill `phase-review` automatiza isso.)
6. **Anexe novas sprints** na planilha (preservando tracking da fase anterior) e atualize o prompt do executor para a próxima fase. Ciclo se repete.

---

## Aprendizados

- **Planilha como fonte única de verdade funciona melhor do que MD.** Status, datas, observações e dropdowns viram naturais na planilha; em MD viraria texto frouxo que o agente "interpreta".
- **Travas explícitas no prompt economizam horas.** A regra "em dúvida, parar e registrar impedimento" evitou refatorações inúteis e decisões prematuras várias vezes — só funcionou porque estava escrita _literalmente_ no prompt, não inferida.
- **Análise por papéis no fim de fase encontra gaps que o execução não vê.** A Sprint 17 só nasceu porque a análise PO/UX/UI mostrou que o blueprint original tinha esquecido a tela de gestão de Contas — backend tinha CRUD, mas nada de UI.
- **"Testes obrigatórios" precisam ser concretos.** "Cobertura adequada" não funciona; "widget test do estado vazio + integração criando X que valida saldo final" funciona. O agente segue exatamente o que está escrito.
- **Não dar autonomia ao agente é o que mais economiza tempo.** Cada decisão tomada por mim em 30 segundos (que o agente teria que adivinhar em 5 minutos com chance de errar) é tempo ganho de duas pontas. Na Sprint 16, o agente parou 3 vezes em sequência (Supabase, deploy, APK Android) pedindo credenciais e decisão de plataforma — a resposta foi "manter local por enquanto" e as 3 tarefas foram canceladas em vez de mal-executadas. Está tudo em [docs/sprints_impedimentos.md](docs/sprints_impedimentos.md).

---

## Status atual

- **Fase 1 (MVP)** — Sprints 0 a 16: **Concluída** (Backend + Mobile + Painel + Deploy inicial; 3 tarefas de deploy/produção canceladas com decisão registrada).
- **Fase 2 (lacunas UX/UI)** — Sprints 17 a 19: **Concluída** (gestão de Contas em todas as superfícies, painel com cadastro completo, polimento UX).
- **Fase 3 (hardening técnico)** — Sprints 20 a 22 + Sprint 23 opcional: **Em andamento**. **Sprint 20** (Hardening de Segurança e Dependências): **Concluída** — Dependabot nos 2 repos, `pip-audit` no CI com gate por severidade, rate limiting por usuário nas mutações, middleware de headers de segurança HTTP, refresh token revogável (+ `/auth/logout`) e política de senha forte. **Sprint 21** (Pipeline Mobile e Observabilidade): **Concluída** — GitHub Actions no repo Flutter (analyze + test + build APK, cobertura ≥ 70%), logging agregado externo, `/healthz` com DB ping e `/metrics` Prometheus-compatible. **Sprint 22** (deploy/backup): **deferida** (DEC-005) — o ambiente segue **local por enquanto** e será reaberta quando houver decisão explícita de subir (mesmo tratamento dado à Sprint 16); o groundwork agnóstico de provedor (`backend/.env.example`, `scripts/pg_backup.sh`, `docs/DEPLOY.md`) foi produzido e commitado, mas a execução real aguarda go humano (DEC-008). **Sprint 23** opcional segue pendente. Motivada pela [análise técnica](analise_architect_qa_security_devops_orcafacil.md).
- **Total acumulado**: 24 sprints planejadas (22 Concluídas + 2 Pendentes, sendo a 22 deferida e a 23 opcional), 105 tarefas no backlog, 75 itens no plano de testes, 8 decisões formais registradas.
- **Nova política a partir da Fase 3** (DEC-003): ao fim de cada fase, o agente executor regera os docs renderizados, atualiza o README, faz commit padronizado e **pausa para confirmação humana antes do `git push`**.

---

_Repositório mantido como portfólio de metodologia. Issues e PRs sobre o método (não sobre o produto) são bem-vindos._
