# OrçaFácil — Análise de Produto e Roadmap (Product Manager)

**Data:** 2026-06-03
**Papel:** Product Manager (PM)
**Escopo:** revisão do produto após o fechamento da **Fase 3** (hardening técnico — DEC-009) para definir as **próximas fases, sprints e features** que entram na planilha e mantêm o agente executor trabalhando.
**Base:** [blueprint](orcafacil_blueprint_completo.md), [planilha de planejamento](orcafacil_planejamento_sprints.xlsx), e as análises anteriores — [PO/UX/UI](analise_po_ux_ui_orcafacil.md), [Architect/QA/Security/DevOps](analise_architect_qa_security_devops_orcafacil.md) e [Backend/Mobile/Frontend](analise_backend_mobile_frontend_orcafacil.md).

> ⚠️ Este é um **documento de proposta**, não execução. Nenhuma sprint abaixo entra na planilha até você aprovar quais entram, quais ficam para depois e quais descarta.

---

## 1. Sumário executivo

O OrçaFácil **completou seu MVP funcional** (Fases 1-2) e **endureceu a base técnica** (Fase 3). Pela ótica de produto, o app faz hoje tudo o que o blueprint prometeu como MVP — mas três coisas seguram a evolução:

1. **🟠 Há promessas do próprio blueprint não cumpridas, e uma delas é um "campo morto".** O campo `is_recurring` existe em banco, schema e formulário mobile ([transactions/models.py:56](backend_OrcamentoFacil/backend/app/transactions/models.py)), mas **não existe motor de recorrência** — o usuário marca "recorrente" e nada se repete. É a feature de maior valor/menor esforço pendente.
2. **🟠 O produto não tem nenhum mecanismo de retorno (engajamento).** Alertas de orçamento (>80%/>100%) são apenas visuais quando o app está aberto; **não há notificações nem lembretes**. Para um app financeiro, engajamento = lançar despesa no momento do gasto — sem lembrete, o uso decai.
3. **🟡 Faltam persona e métricas de sucesso.** O blueprint lista features, mas **não define para quem** (persona) nem **como medir sucesso** (KPIs/ativação/retenção). Sem isso, qualquer roadmap é "lista de features", não produto.

Além disso, a Fase 4 (qualidade de código) já está proposta na [análise de engenharia](analise_backend_mobile_frontend_orcafacil.md) e o deploy (Fase 3 / Sprint 22) segue **deferido** por decisão sua (DEC-005/008) — ambos entram no sequenciamento abaixo.

**Proposta:** 1 fase de débito técnico já existente (Fase 4) + **3 novas fases de produto** (Fases 5, 6, 7) + backlog estratégico.

---

## 2. Estado atual — cobertura de features (blueprint × construído)

| Feature do blueprint | Backend | Mobile | Painel | Acessível ao usuário? |
|---|---|---|---|---|
| Autenticação (JWT, refresh revogável) | ✅ | ✅ | ✅ | ✅ |
| Gestão de Contas | ✅ | ✅ | ✅ | ✅ |
| Categorias (cor/ícone) | ✅ | ✅ | ❌ (sem página) | ⚠️ só mobile |
| Transações (CRUD, filtros, busca) | ✅ | ✅ | ✅ | ✅ |
| Orçamentos mensais + alertas | ✅ | ✅ | ❌ (sem página) | ⚠️ só mobile |
| Metas financeiras | ✅ | ✅ | ❌ (sem página) | ⚠️ só mobile |
| Dashboard (summary/breakdown/cashflow) | ✅ | ✅ | ✅ | ✅ |
| Importação CSV (categorização por keyword) | ✅ | ✅ | ✅ | ✅ |
| **Transações recorrentes (`is_recurring`)** | ⚠️ campo existe, sem motor | ⚠️ checkbox sem efeito | ❌ | ❌ **promessa não cumprida** |
| **Notificações / lembretes** | ❌ | ❌ | — | ❌ |
| **Exportação PDF / relatório compartilhável** | ❌ | ❌ | ⚠️ só CSV/XLSX | ⚠️ parcial |
| **Projeção / previsão de saldo (Fase 8 IA)** | ❌ | ❌ | ❌ | ❌ |
| **Categorização inteligente (ML, Fase 8 IA)** | ⚠️ só keyword | ❌ | ❌ | ❌ |
| **Insights / sugestões de economia (Fase 8 IA)** | ❌ | ❌ | ❌ | ❌ |
| **Multiusuário familiar** | ❌ | ❌ | ❌ | ❌ |
| **Deploy público / distribuição (APK/Store)** | ⚠️ groundwork (S22 deferida) | ❌ | ❌ | ❌ (local) |

**Planejamento (planilha):** 24 sprints (0-23). Fases 1-2 = 20 sprints concluídas; Fase 3 = S20+S21 concluídas, S22 deferida, S23 opcional. 9 decisões formais.

---

## 3. Análise — Product Manager

### O que está bem
- **MVP coerente e completo.** Todas as 8 features-núcleo do blueprint existem e são usáveis (no mobile). O produto cobre o ciclo: cadastrar conta → lançar → orçar → metas → visualizar.
- **Qualidade como trava de produto.** Cobertura backend 96,6%, bateria de regressão de saldo perene, CI nos dois repos. Isso permite evoluir sem medo de quebrar o que existe — um diferencial real para a velocidade das próximas fases.
- **Evolução guiada por análise.** A própria existência de Fases 2 e 3 nasceu de análises de fim de fase. Há cultura de roadmap iterativo.

### Problemas / lacunas de produto
1. **Promessas do blueprint não entregues.** `is_recurring` (campo morto), notificações push, exportação PDF, multiusuário, e toda a "Fase 8 — IA" do blueprint. Algumas são MVP-adjacentes (recorrência), outras são roadmap futuro (IA, multiusuário).
2. **Sem gatilho de retorno.** Nenhuma notificação/lembrete. Para finanças pessoais, o momento de valor é o do gasto — sem lembrete, o lançamento não acontece e o dado fica incompleto.
3. **Painel sem paridade total.** Orçamentos, Metas e Categorias não têm página no painel ([panel/pages/](backend_OrcamentoFacil/panel/pages/) tem só 5 páginas). O usuário desktop não gerencia tudo.
4. **Profundidade analítica rasa.** O dashboard mostra o passado (resumo/breakdown/cashflow), mas **não projeta o futuro** nem dá comparativo mês-a-mês ou insights — exatamente onde mora o valor percebido de um app financeiro maduro.
5. **Sem persona nem métricas.** Não há definição de para quem o app é, nem de ativação/retenção/north-star. Isso deveria preceder qualquer nova feature.
6. **Produto sem usuários reais.** Deploy deferido (decisão consciente sua). Enquanto local, todo feedback é proxy (testes/análise). Reabrir o deploy é o que transforma "portfólio" em "produto medível" — quando você decidir.

### Recomendações PM
- **R1:** Definir **persona + métricas de sucesso** antes da Fase 5 (artefato leve, 1 página: persona, ativação = "1ª conta + 1ª transação em D0", north-star = "transações lançadas/semana", retenção D7/D30).
- **R2:** Priorizar **recorrência** (Fase 5) — maior valor/menor esforço, fecha promessa do blueprint e enriquece todo o resto (dashboard, projeção).
- **R3:** Adicionar **engajamento** (notificações/lembretes) logo após — é o que sustenta o uso diário.
- **R4:** **Pagar o débito técnico (Fase 4) antes de empilhar features de produto** — adicionar recorrência/IA sobre boilerplate manual no mobile e backend síncrono multiplica o custo (ver [análise de engenharia](analise_backend_mobile_frontend_orcafacil.md)).
- **R5:** Tratar **IA (Fase 6)** e **deploy/distribuição (Fase 7)** como fases próprias, sequenciadas; **multiusuário** e **offline** como backlog estratégico (grandes, dependem de decisão).

---

## 4. Resposta direta — quais são as próximas fases?

Sequência recomendada (numeração continua a planilha):

| Fase | Tema | Sprints | Natureza | Pré-condição |
|---|---|---|---|---|
| **Fase 4** | Qualidade de código sustentável | 24-27 | Débito técnico (já proposta) | Aprovar a [análise de engenharia](analise_backend_mobile_frontend_orcafacil.md) |
| **Fase 5** | Recorrência, Engajamento e Relatórios | 28-30 | Produto (valor ao usuário) | Fase 4 (recomendado) + R1 |
| **Fase 6** | Inteligência Financeira (Fase 8 IA do blueprint) | 31-33 | Produto (diferenciação) | Fase 5 (recorrência alimenta projeção) |
| **Fase 7** | Deploy, Distribuição e Operação | reabre 22 + 34 | Operação | **"go" humano de deploy** (DEC-005) |
| Backlog | Multiusuário familiar; Offline-first | a definir | Produto (grande) | Decisão estratégica |

> **Sequenciamento PM:** Fase 4 → Fase 5 → Fase 6, com a Fase 7 (deploy) podendo ser puxada para frente a qualquer momento se você decidir tornar o app público (ela é independente das features). A recorrência (Fase 5) é pré-requisito natural da projeção de fluxo de caixa (Fase 6).

---

## 5. Oportunidades consolidadas e priorizadas

| # | Sev/Prio | Oportunidade | Evidência | Entra em |
|---|---|---|---|---|
| O1 | 🟠 Alta | Recorrência real (ativar `is_recurring`) | [models.py:56](backend_OrcamentoFacil/backend/app/transactions/models.py) campo sem motor | Fase 5 / S28 |
| O2 | 🟠 Alta | Notificações + lembretes | sem `flutter_local_notifications`; alertas só visuais | Fase 5 / S29 |
| O3 | 🟡 Média | Relatório PDF + paridade do painel | painel só CSV/XLSX; sem páginas de Orçamento/Meta/Categoria | Fase 5 / S30 |
| O4 | 🟡 Média | Persona + métricas de sucesso | ausentes no blueprint | Pré-Fase 5 (R1) |
| O5 | 🟡 Média | Projeção de fluxo de caixa + comparativo mensal | dashboard só retrospectivo | Fase 6 / S32 |
| O6 | 🟢 Baixa | Categorização inteligente | só keyword no import | Fase 6 / S31 |
| O7 | 🟢 Baixa | Insights / sugestões de economia | inexistente | Fase 6 / S33 |
| O8 | 🟠 (gated) | Deploy público / distribuição | S22 deferida (DEC-005) | Fase 7 (aguarda "go") |
| O9 | 🟢 Futuro | Multiusuário familiar | nome do repo sugere; inexistente | Backlog |
| O10 | 🟢 Futuro | Offline-first | proposto na Fase 4 (S27 opcional) | Backlog / Fase 4 opc. |

---

## 6. Sprints propostas

> Padrão idêntico à planilha: ID `Snn-Tnn`, tipo, prioridade, descrição acionável, critérios verificáveis, dependências, **testes obrigatórios concretos**. Regra perene mantida: **`test_balance_regression_full_flow` continua verde** após qualquer sprint que toque em saldo.

### Fase 4 — Qualidade de Código Sustentável (Sprints 24-27)
**Já detalhada** na [análise de engenharia](analise_backend_mobile_frontend_orcafacil.md) (seção 7). Resumo para sequenciamento:
- **S24** — Async no backend (`AsyncSession`), Unit-of-Work na borda, `*Base` schemas, índices derivados de queries, mensagens PT-BR, paginação em budgets/goals.
- **S25** — `freezed` + `json_serializable` + `build_runner` no mobile; `AsyncView` compartilhado; cache HTTP no Dio; app icon/splash.
- **S26** — DRY no painel: `services/` com cache, `transaction_form` compartilhado, persistência de filtros.
- **S27 (opcional)** — i18n + offline-first (Hive) + deep linking.

*Recomendação PM: aprovar e executar antes da Fase 5. Detalhamento de tarefas/testes já existe no documento citado.*

---

### Fase 5 — Recorrência, Engajamento e Relatórios

#### Sprint 28 — Transações Recorrentes (ativar `is_recurring`) — fecha O1
**Objetivo:** transformar o campo morto `is_recurring` em recorrência real: o usuário define uma regra (frequência/fim) e o sistema gera os lançamentos automaticamente, respeitando a regra crítica de saldo.
**Entregáveis:** modelo de série recorrente + migration; motor de geração idempotente; CRUD de séries; UI mobile; (opcional) painel.

| ID | Tipo | Prio | Descrição | Critérios de aceitação | Dep. | Testes obrigatórios |
|---|---|---|---|---|---|---|
| S28-T01 | Backend/Banco | Alta | Modelar série recorrente: `recurrence` (frequência: weekly/monthly/yearly, intervalo, data início, fim por data OU nº de ocorrências, `next_run`). Migration Alembic reversível. | `alembic upgrade/downgrade head` ok; constraints (frequência válida, fim XOR ocorrências); FK para conta/categoria/usuário. | Fase 4 (S24) | Unit do modelo + teste de migration reversível. |
| S28-T02 | Backend | Alta | Motor de geração: materializa lançamentos das séries vencidas até hoje, reutilizando a regra de saldo existente. Idempotente. | Gera N ocorrências corretas; rodar 2x **não duplica**; saldo da conta atualizado corretamente; série pausada não gera. | S28-T01 | Integração gerando 3 meses; **idempotência (2ª execução = 0 novos)**; **`test_balance_regression_full_flow` verde**. |
| S28-T03 | Backend | Alta | CRUD de séries: criar/editar/pausar/excluir. Excluir série **não** apaga lançamentos já gerados. | Isolamento por usuário (404 cross-user); pausar interrompe geração; editar afeta só ocorrências futuras. | S28-T01 | Integração CRUD + isolamento; pausar/retomar. |
| S28-T04 | Mobile | Alta | UI de recorrência no form de transação (toggle + frequência + fim) e tela "Recorrentes" listando séries com ação pausar/excluir. | Form salva regra; lista mostra próxima data; estado vazio tratado. | S28-T02, S25 | Widget test do form de recorrência + estado vazio da lista. |
| S28-T05 | Painel | Média | Seção "Recorrentes" no painel (listar/pausar) — paridade desktop. | Lista séries do usuário; pausar reflete no backend. | S28-T03 | Unit com mock da `api.py`. |

#### Sprint 29 — Notificações e Lembretes — fecha O2
**Objetivo:** dar ao app um gatilho de retorno: alertas de orçamento e metas viram notificações locais, e o usuário recebe lembrete de lançamento.
**Entregáveis:** preferências de notificação (backend); integração `flutter_local_notifications`; alertas de orçamento/meta; lembrete diário configurável.

| ID | Tipo | Prio | Descrição | Critérios de aceitação | Dep. | Testes obrigatórios |
|---|---|---|---|---|---|---|
| S29-T01 | Backend/Banco | Média | Preferências de notificação por usuário (alertas orçamento on/off, lembrete diário on/off + horário). Migration. | CRUD isolado por usuário; defaults sensatos. | Fase 4 (S24) | Integração CRUD prefs + isolamento. |
| S29-T02 | Mobile | Alta | Integrar `flutter_local_notifications` (canal, permissão Android 13+, init). | Permissão solicitada; canal criado; notificação de teste dispara. | S25 | Smoke com plugin mockado. |
| S29-T03 | Mobile | Alta | Disparar notificação local quando uma transação leva o orçamento a >80%/>100%, e quando uma meta é atingida. | Gatilho correto no cruzamento de limite; não dispara duplicado no mesmo limite/mês. | S29-T02 | Unit/widget do gatilho (limite cruzado → 1 notificação). |
| S29-T04 | Mobile | Média | Lembrete diário de lançamento agendado, configurável em Configurações (usa prefs de S29-T01). | Agenda no horário escolhido; desligar cancela; persiste após restart. | S29-T02 | Unit do agendamento (liga/desliga/horário). |

#### Sprint 30 — Relatórios Exportáveis e Paridade do Painel — fecha O3
**Objetivo:** entregar um relatório mensal compartilhável (PDF) e fechar a paridade do painel (Orçamentos, Metas, Categorias).
**Entregáveis:** endpoint de relatório consolidado; export PDF no painel; páginas faltantes do painel; export/compartilhar no mobile.

| ID | Tipo | Prio | Descrição | Critérios de aceitação | Dep. | Testes obrigatórios |
|---|---|---|---|---|---|---|
| S30-T01 | Backend | Média | Endpoint `/reports/monthly` consolidando resumo + breakdown + orçamentos + metas em um payload. | Período vazio retorna zeros; período cheio bate com somatórios das transações. | Fase 4 (S24) | Integração período vazio + período com dados. |
| S30-T02 | Painel | Média | Exportação **PDF** do relatório mensal (com gráficos) via reportlab/WeasyPrint. | PDF gerado não vazio; contém resumo + gráfico; nome com mês/ano. | S30-T01 | Teste gera PDF (>0 bytes, content-type ok). |
| S30-T03 | Painel | Média | Páginas de **Orçamentos**, **Metas** e **Categorias** no painel (CRUD), fechando paridade com o mobile. | CRUD funcional contra backend; isolamento por usuário. | — | Unit por página com mock da `api.py`. |
| S30-T04 | Mobile | Baixa | Compartilhar resumo mensal (share sheet com PDF/imagem). | Botão "Compartilhar" abre share sheet com arquivo válido. | S30-T01, S25 | Widget test do botão de compartilhar. |

---

### Fase 6 — Inteligência Financeira (Fase 8 "IA" do blueprint)

> Materializa a "Fase 8 — IA Futuramente" do blueprint. Começa por heurística/estatística (sem dependência de LLM); o assistente conversacional fica como tarefa opcional.

#### Sprint 31 — Categorização Inteligente — fecha O6
- **S31-T01 (Backend, Alta):** `/categories/suggest` sugere categoria a partir da descrição usando histórico do usuário + keywords. *Testes: descrição conhecida → categoria correta; descrição nova → fallback "Outros".*
- **S31-T02 (Mobile, Média):** auto-preencher categoria sugerida no form de transação (editável). *Testes: widget — sugestão aparece e é sobreescrevível.*
- **S31-T03 (Backend/Import, Média):** aplicar sugestão no import CSV, elevando a taxa de auto-categorização. *Testes: import de fixture categoriza ≥ X% (definir baseline).*

#### Sprint 32 — Projeção e Comparativos — fecha O5
- **S32-T01 (Backend, Alta):** `/dashboard/forecast` projeta saldo dos próximos N meses (recorrentes confirmados + média móvel das despesas). *Testes: projeção determinística com fixture; recorrentes refletidos.*
- **S32-T02 (Mobile, Média):** gráfico de projeção no dashboard (fl_chart). *Testes: widget renderiza série projetada.*
- **S32-T03 (Backend, Média):** comparativo mês-a-mês (orçado × realizado, variação %). *Testes: integração com 2 meses de dados.*

#### Sprint 33 — Insights e Sugestões — fecha O7
- **S33-T01 (Backend, Média):** `/insights` com regras (categoria que mais cresceu, gasto acima da média, meta em risco, orçamento perto do limite). *Testes: cada regra com fixture que a dispara e uma que não.*
- **S33-T02 (Mobile, Média):** card de insights no dashboard. *Testes: widget com 0 e com N insights.*
- **S33-T03 (opcional):** assistente conversacional (LLM) sobre os dados do usuário — **flag/backlog**, só com aprovação explícita (custo + privacidade).

---

### Fase 7 — Deploy, Distribuição e Operação (aguarda "go" humano)

> **Gated pela DEC-005.** Não executar sem sua decisão explícita de subir. O groundwork agnóstico de provedor já está commitado (`.env.example`, `pg_backup.sh`, `DEPLOY.md`).

- **Reabrir Sprint 22** (S22-T01..T06): Postgres gerenciado, deploy backend, deploy painel, APK release, backup validado por restore, `DEPLOY.md` finalizado. *Testes: já definidos na planilha (alembic remoto, smoke_prod verde, restore do backup).*
- **Sprint 34 — Play Store e Analytics de Produto:** publicação na Play Store (closed testing) + instrumentação de eventos (ativação, retenção, funil) e definição de KPIs (R1). *Testes: smoke de release; evento de ativação registrado.*

---

### Backlog estratégico (sem fase atribuída)
- **Multiusuário familiar** — orçamento/contas compartilhadas com papéis. Grande: exige redesenho de ownership → membership + permissões + convites. Coerente com o nome do projeto; merece fase própria e decisão de produto antes de planejar.
- **Offline-first** — já contemplado na Fase 4 (S27 opcional, Hive). Promover a fase própria se virar prioridade.
- **OCR de comprovantes / Integração bancária (Open Finance)** — citados no blueprint como "melhorias futuras"; alto custo/regulação, manter no radar, fora do horizonte próximo.

---

## 7. Riscos e considerações de implementação

- **Recorrência (S28) toca saldo — risco de regressão.** O motor de geração reusa a regra crítica de saldo; qualquer erro reflete em `current_balance`. A bateria de regressão é a rede de segurança; idempotência é o teste mais importante (gerar 2x não pode duplicar).
- **Dependência da Fase 4.** Construir recorrência/notificações sobre o backend síncrono e o mobile com boilerplate manual aumenta o custo. PM recomenda Fase 4 antes — mas se houver pressa por valor visível, S28 é executável sem a Fase 4 (só mais cara de manter).
- **Notificações dependem de plataforma.** `flutter_local_notifications` exige permissão (Android 13+) e canais; teste real só no device. CI valida com mock; smoke manual no fim da S29.
- **Projeção (S32) depende de recorrência (S28).** Sem recorrentes confirmados, a projeção vira só média móvel — menos útil. Respeitar a ordem das fases.
- **IA conversacional (S33-T03) tem custo e privacidade.** Mandar dados financeiros a um LLM exige decisão explícita; por isso é opcional/flag.
- **Deploy (Fase 7) continua sendo decisão sua.** Nada de produção sem "go" (DEC-005). As features das Fases 5-6 rodam 100% local.
- **Falta persona/KPIs (R1).** Sem isso, priorização vira preferência. Recomendo o artefato leve antes de abrir a Fase 5.

---

## 8. Próximos passos (o que você decide antes de virar planejamento)

1. **Aprovar o sequenciamento de fases** (4 → 5 → 6 → 7) ou reordenar (ex.: puxar deploy para frente).
2. **Decidir o escopo da Fase 5** — quais das 3 sprints (28 recorrência / 29 notificações / 30 relatórios) entram e em que ordem.
3. **Definir persona + métricas (R1)** — eu posso gerar o artefato de 1 página antes de abrir a Fase 5.
4. **Confirmar a Fase 4** (qualidade de código) como pré-requisito ou paralelizar.
5. Após o "OK", as sprints aprovadas são anexadas à planilha (via skill `sprint-planning` ou edição direta), **preservando todo o tracking das Fases 1-3**, com nome prefixado por `[FASE n]`, e o `PROMPT_INICIAL_CLAUDE_CODE.md` é atualizado para a nova fase.
6. Registrar **DEC-010** justificando a abertura da próxima fase aprovada. Documento commitado; push aguarda OK humano (DEC-003).
