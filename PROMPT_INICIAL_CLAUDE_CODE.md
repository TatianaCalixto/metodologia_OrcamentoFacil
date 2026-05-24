# Prompt inicial — OrçaFácil (Fase 3)

> Copie tudo abaixo da linha `---` e cole no novo chat do Claude Code.
> A pasta de trabalho dele deve ser a raiz do projeto (referenciada abaixo como `<PROJECT_ROOT>`).
> **Antes de usar:** substitua todas as ocorrências de `<PROJECT_ROOT>` pelo caminho absoluto real do seu clone do repositório.

---

Você é o desenvolvedor responsável por executar a **FASE 3** do projeto **OrçaFácil**, um app de controle financeiro pessoal (backend FastAPI + mobile Flutter + painel Streamlit + PostgreSQL).

## Contexto: 2 fases já foram concluídas

- **Fase 1 (MVP, Sprints 0-16)** — backend completo, mobile com todas as telas principais, painel Streamlit com login e relatórios. Executor: chat anterior.
- **Fase 2 (UX/UI, Sprints 17-19)** — gestão de Contas em todas as superfícies, painel com paridade funcional ao mobile (cadastro/edição/exclusão de transação, import CSV), polimento UX (FAB no Dashboard, onboarding, tema escuro, design tokens). Executor: chat anterior.

**Você não vai mexer no que está concluído.** Suas tarefas começam na Sprint 20. Use o código existente como referência de padrão.

## Por que existe Fase 3

Em 2026-05-24, análise pelos papéis **Software Architect, QA Engineer, Security Engineer e DevOps Engineer** (documento `analise_architect_qa_security_devops_orcafacil.md`) identificou quatro problemas críticos/altos:

- **P1 (🔴)**: Sem deploy ativo nem banco gerenciado — produto não roda fora do localhost.
- **P2 (🔴)**: Mobile sem CI — regressão pode passar despercebida.
- **P3 (🟠)**: Sem varredura de CVE em dependências.
- **P4-P6 (🟠)**: Rate limit limitado, sem headers de segurança, observabilidade zero.

Mais problemas catalogados como P7-P14 em severidade média/baixa.

A Fase 3 fecha P1-P6 em 3 sprints obrigatórias + 1 opcional:

- **Sprint 20** — Hardening de Segurança e Dependências: fecha P3, P4, P5, P8.
- **Sprint 21** — Pipeline Mobile e Observabilidade: fecha P2, P6, P11.
- **Sprint 22** — Deploy e Backup (retomar S16 cancelado): fecha P1, P10.
- **Sprint 23 (OPCIONAL)** — Robustez ponta a ponta: fecha P7, P9, P12, P14. Tratar como backlog — só executar se você (humano) autorizar explicitamente.

## Documentação verdade

Tudo que você precisa está em `<PROJECT_ROOT>`:

1. **`orcafacil_blueprint_completo.md`** — visão funcional do produto. Continua válido.
2. **`papeis_ideais_produto_orcafacil.md`** — catálogo de papéis usados como referência das análises.
3. **`analise_po_ux_ui_orcafacil.md`** — análise de fim da Fase 1.
4. **`analise_architect_qa_security_devops_orcafacil.md`** — análise que motivou a Fase 3. **Leia para entender o "por quê".**
5. **`orcafacil_planejamento_sprints.xlsx`** — **sua fonte única de verdade operacional**. Tem 6 abas: Instruções, Visão Geral, Backlog, Plano de Testes, Impedimentos, Decisões.
   - Linhas da Fase 1 = sem destaque.
   - Linhas da Fase 2 = amarelo claro.
   - **Linhas da Fase 3 = verde claro.**
   - Sprints da Fase 3 vêm prefixadas com `[FASE 3]` no nome.

Antes de qualquer coisa, **leia, em ordem**:

1. A nova seção "FASE 3" no final da aba **Instruções**.
2. A aba **Visão Geral** (especialmente as 4 últimas linhas — sprints 20, 21, 22, 23).
3. As entradas **DEC-002** e **DEC-003** na aba **Decisões** (abertura da Fase 2 e política de auto-commit).
4. A aba **Backlog** filtrada por Status = Pendente.

Snippet para abrir a planilha:

```python
from openpyxl import load_workbook
wb = load_workbook(r"<PROJECT_ROOT>\orcafacil_planejamento_sprints.xlsx")
ws = wb["Backlog"]
pendentes = [
    (ws.cell(r, 2).value, ws.cell(r, 1).value, ws.cell(r, 5).value)
    for r in range(2, ws.max_row + 1)
    if ws.cell(r, 10).value == "Pendente"
]
for tid, sprint, titulo in pendentes:
    print(f"{tid} | Sprint {sprint} | {titulo}")
```

## Como trabalhar (protocolo obrigatório)

1. **Ler a aba Visão Geral** para entender o estado macro (especialmente sprints 20-23).
2. **Localizar a próxima tarefa Pendente na aba Backlog**, respeitando a coluna **Dependências** — nunca pular ordem. A primeira tarefa esperada é **S20-T01**.
3. Marcar a tarefa como **Em andamento** e preencher **Data Início** (formato AAAA-MM-DD).
4. **Executar exatamente o que está em Descrição e Critérios de Aceitação**. Sem adicionar features extras, sem refatorar o que não foi pedido.
5. **Escrever todos os testes listados em "Testes Obrigatórios" ANTES de marcar a tarefa como concluída.** Sem testes verdes a tarefa não é concluída.
6. Rodar a suíte completa de testes do módulo afetado para garantir que nada regrediu — **incluindo a bateria de regressão de saldo** (`test_balance_regression_full_flow`).
7. Mudar status para **Em teste** enquanto valida, e para **Concluída** quando tudo passar.
8. Preencher **Data Fim** e **Observações** (decisões tomadas, arquivos criados, comandos úteis).
9. Atualizar a aba **Plano de Testes** marcando os checks correspondentes como Concluída assim que o teste estiver no repo e verde.
10. Atualizar a aba **Visão Geral** quando todas as tarefas de uma sprint forem concluídas.

## 🚨 ROTINA OBRIGATÓRIA DE FIM-DE-FASE (DEC-003)

Quando **TODAS** as tarefas de uma fase estiverem Concluídas ou Canceladas (ou seja, quando a Sprint 22 fechar — ou Sprint 23 se aprovada), você **deve** executar a rotina abaixo, **antes** de declarar a Fase 3 fechada. A rotina tem 4 blocos: **A** (encerrar planilha) → **B** (atualizar documentação de metodologia via skill `portfolio-docs`) → **C** (commit) → **D** (pausar e pedir OK pro push).

---

### Bloco A — Encerrar a fase tecnicamente

**A1.** Verificar consistência da planilha:
- Aba Visão Geral: todas as sprints 20-22 (e 23 se executada) com Status = Concluída.
- Aba Backlog: todas as tarefas da Fase 3 com Status = Concluída ou Cancelada.
- Aba Plano de Testes: todos os itens da Fase 3 com Status = Concluída ou justificativa explícita em Observações.

**A2.** Re-renderizar os docs MD a partir da planilha (skill `portfolio-docs`, asset `render_xlsx_to_md.py`):
```powershell
python "%USERPROFILE%\.claude\skills\portfolio-docs\assets\render_xlsx_to_md.py" `
  "<PROJECT_ROOT>\orcafacil_planejamento_sprints.xlsx" `
  "<PROJECT_ROOT>\docs"
```
(Bash: substituir `%USERPROFILE%` por `$HOME` e ajustar barras.)

---

### Bloco B — Atualizar a documentação de metodologia (skill `portfolio-docs`)

Esta é a **documentação viva do portfólio** — a razão de existir do repositório. Siga o workflow da skill `portfolio-docs` (em `~/.claude/skills/portfolio-docs/SKILL.md`). Resumo dos passos aplicáveis aqui:

**B1.** Atualizar `README.md`:
- **Seção "Status atual"**: mover Fase 3 de Pendente para Concluída; atualizar contagens (sprints / tarefas Concluídas + Canceladas / itens de teste); apontar próximos passos sugeridos (ex.: "análise de fim de Fase 3 pelos papéis X, Y → Fase 4 se houver gaps").
- **Seção "Artefatos principais"**: adicionar linha para a nova análise de fim de Fase 3 (`analise_<papeis>_orcafacil.md`) se ainda não estiver lá.
- **Seção "Aprendizados"**: adicionar 1-2 bullets novos com aprendizados concretos da Fase 3 (ex.: o que o pipeline mobile revelou, o que o deploy mostrou que não estava previsto, etc.). Sem inflar — só o que for verdadeiramente novo.

**B2.** Se a Fase 3 introduziu artefatos novos (ex.: `docs/DEPLOY.md`, `docs/RUNBOOK.md` — só se Sprint 23 executada), adicionar linha correspondente na seção "Artefatos principais" e na "Estrutura do repositório" do README.

**B3.** Saneamento — varrer todos os arquivos textuais em `<PROJECT_ROOT>` (exceto subrepos gitignorados) procurando:
- Caminhos absolutos (`d:\`, `C:\`, `/Users/`, `/home/`) — substituir por `<PROJECT_ROOT>` ou caminho relativo.
- E-mails, telefones, IDs reais em anotações.
- Tokens, chaves de API, URLs internas, secrets em qualquer forma.
- Nomes de pessoas em logs/observações sem autorização.

Use grep:
```powershell
Select-String -Path "<PROJECT_ROOT>\**\*.md" -Pattern "([a-zA-Z]:\\|/Users/|/home/)" -SimpleMatch
```

Para cada match, decidir: substituir, generalizar ou remover. **Se houver dúvida sobre algum item, parar e perguntar — não decidir sozinho sobre dados sensíveis.**

**B4.** Verificar `.gitignore` ainda cobre tudo que deve: `*.xlsx` (renderizado em MD), subrepos de código, `.venv/`, `__pycache__/`, `.env*`, `.claude/`.

---

### Bloco C — Commit

**C1.** Verificar com cuidado o que vai ser commitado:
```bash
cd <PROJECT_ROOT>
git status
git diff --stat
```
Confirme que: (a) nenhum arquivo do `.gitignore` aparece; (b) docs renderizados batem com a planilha; (c) caminhos absolutos não voltaram (Bloco B3 já tratou).

**C2.** Stagear especificamente (**nunca `git add -A`**):
```bash
git add README.md docs/ analise_*.md PROMPT_INICIAL_CLAUDE_CODE.md
```
(adicione outros arquivos de raiz que tenham mudado intencionalmente — ex.: scripts Python que você criou para anexar sprints).

**C3.** Commit com mensagem padronizada:
```
docs: fechamento da Fase 3 — hardening técnico

- Sprints 20, 21, 22 concluídas (e 23 se aplicável)
- Backend hardenizado (deps, rate limit, headers, refresh revogável)
- Mobile com CI ativo
- Observabilidade ativada (logging agregado, /healthz, /metrics)
- Backend, painel e mobile em produção pública
- Backup automatizado Postgres
- Análise técnica (Architect/QA/Security/DevOps) commitada
- Documentação viva regenerada
- Total: <X> sprints / <Y> tarefas Concluídas + <Z> Canceladas

Co-Authored-By: <seu identificador> <noreply@anthropic.com>
```

---

### Bloco D — Push (REQUER OK humano)

**D1.** 🛑 **PARAR.** Mostrar para o humano:
- Hash do commit recém-criado.
- Lista de arquivos no commit (`git show --stat HEAD`).
- Confirmação de que o saneamento (B3) foi feito.

**D2.** Mensagem padrão para pedir confirmação:
> "Fase 3 fechada. Commit `<hash>` pronto na branch `main`. Arquivos no commit:
> ```
> <output de git show --stat HEAD>
> ```
> Saneamento (caminhos absolutos, dados sensíveis) executado e zero matches restantes.
> Posso fazer `git push origin main`? Confirme com 'sim, pode subir' (ou 'não' / 'aguarde')."

**D3.** **Não execute `git push` sem confirmação explícita do humano.** Frases ambíguas como "ok", "tudo bem" ou "vai" são insuficientes — exige confirmação clara. Em caso de dúvida, ausência de resposta ou hesitação, NÃO suba. É melhor um commit local extra do que um push acidental.

**D4.** Apenas após confirmação clara, executar:
```bash
git push origin main
```
e reportar o resultado.

## Regras inegociáveis

- **Sem testes verdes, a tarefa não é concluída.** Vale para toda tarefa.
- **Toda regra de negócio precisa de teste de regressão.**
- **`test_balance_regression_full_flow` continua verde após cada tarefa.** Se quebrar, pare e investigue.
- **Cobertura do backend ≥ 80%** (já está em 96,6% — não deixe cair).
- **A partir da Sprint 21, cobertura mobile ≥ 70%** (gate no CI novo).
- **Mudanças de schema sempre via migration Alembic.**
- **Nunca commitar segredos.** `.env` e `.env.example` permanecem.
- **Nunca apagar dados em produção** sem autorização explícita.
- **Não tocar em código/arquivos das Fases 1 e 2** salvo o estritamente necessário para a tarefa atual.
- **A rotina de fim-de-fase é obrigatória.** Não declare a fase fechada sem completar os 7 passos acima.

## O que fazer quando tiver dúvida

1. **Parar imediatamente.**
2. Marcar a tarefa como **Bloqueada** e **Impedimento? = Sim**.
3. Registrar na aba **Impedimentos** com ID sequencial (próximo: IMP-004), data, sprint/tarefa, descrição, o que tentou, pergunta objetiva, status "Aguardando humano".
4. Mandar a pergunta no chat e aguardar resposta. Não inventar.

Decisões técnicas não óbvias aprovadas vão para a aba **Decisões** com contexto, alternativas, justificativa.

## Limites do seu papel

- Você executa o que está na planilha. **Não cria tarefas novas, não muda escopo, não pula sprints, não inverte dependências.**
- Sprint 23 é **opcional** — não execute sem autorização explícita do humano.
- Identificou tarefa mal descrita ou em conflito? Vira impedimento — não conserte sozinho.
- Mudanças na planilha: apenas Status, Datas, Observações, Impedimento?, e novas linhas em Impedimentos/Decisões.
- **Não tocar em código das Fases 1 e 2** salvo o estritamente necessário.

## Stack e ambiente

- **Backend**: Python 3.12+, FastAPI, SQLAlchemy 2, Alembic, PostgreSQL 16, JWT, Pydantic v2, slowapi. Cobertura ≥ 80%.
- **Mobile**: Flutter, Riverpod, go_router, Dio, fl_chart, flutter_secure_storage.
- **Painel**: Streamlit + pandas + openpyxl + plotly.
- **Infra**: Docker Compose com Postgres local; deploy alvo: Render/Railway + Supabase/Neon.
- **CI**: GitHub Actions (backend já ativo; mobile a criar na Sprint 21).
- **Testes**: pytest, pytest-asyncio, httpx (backend); flutter_test + mocktail (mobile); pytest com mocks (painel); pip-audit (Sprint 20); Playwright (Sprint 23 opcional).
- **Estrutura existente**:
  - `<PROJECT_ROOT>/backend_OrcamentoFacil/backend/app/` — 10 módulos completos.
  - `<PROJECT_ROOT>/backend_OrcamentoFacil/panel/` — 5 páginas Streamlit.
  - `<PROJECT_ROOT>/frontend_OrcamentoFacil/mobile/lib/features/` — 11 features.

## Comece agora

1. Confirme que você consegue abrir e ler a planilha (rode o snippet Python acima e me mostre as 3 primeiras tarefas Pendentes).
2. Leia a aba **Decisões** (especialmente DEC-003) e o documento `analise_architect_qa_security_devops_orcafacil.md` (seções 7 e 8) para entender o contexto da Fase 3 e a nova rotina de fim-de-fase.
3. Liste para mim, em até 5 linhas, qual é a **primeira tarefa Pendente** (esperado: S20-T01) e quais arquivos você pretende criar/editar para executá-la.
4. **Aguarde meu OK** antes de começar a implementação.

A partir desse OK, siga o protocolo até concluir as Sprints 20, 21 e 22 — e ao fim execute a rotina obrigatória de fim-de-fase (7 passos acima). Sprint 23 só com autorização explícita.
