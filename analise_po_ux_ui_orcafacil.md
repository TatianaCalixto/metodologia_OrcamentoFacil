# OrçaFácil — Análise PO / UX / UI

**Data:** 2026-05-24
**Escopo:** revisão do produto a partir dos papéis de Product Owner, UX Designer e UI Designer.
**Base:** [blueprint](orcafacil_blueprint_completo.md), [planejamento de sprints](orcafacil_planejamento_sprints.xlsx) e código já implementado em `backend_OrcamentoFacil/` (backend + painel Streamlit) e `frontend_OrcamentoFacil/mobile/` (Flutter).

---

## 1. Sumário executivo

O projeto está tecnicamente sólido — backend FastAPI completo, mobile Flutter com a maior parte das telas, painel Streamlit com login e relatórios. Mas, olhando como produto, há **três lacunas de UX que comprometem o uso real**:

1. **Não há tela de cadastro/edição de contas (accounts) em lugar nenhum.** O backend expõe o CRUD, mas nem mobile nem painel oferecem essa tela. Como toda transação exige `account_id`, o usuário fica preso: sem conta, não consegue cadastrar nada. Hoje só dá para criar contas via Swagger / chamada direta à API.
2. **O painel web só lê e exporta — não permite cadastrar entradas/saídas nem importar CSV.** A sua percepção está parcialmente correta: o painel não tem cadastro manual de transação. (Diferente do app mobile, onde existe formulário completo.)
3. **O Dashboard mobile não tem atalho para "Nova transação".** A ação mais frequente do app (lançar uma despesa) exige 3 passos: abrir o drawer ou tocar no ícone "Transações" → ir para a lista → tocar no FAB "+". Atrito alto para a tarefa primária.

Há também problemas menores de consistência visual, acessibilidade e priorização de roadmap. Detalhe abaixo.

---

## 2. Estado atual — o que existe vs. o que o blueprint prometeu

### Backend (FastAPI)

| Módulo | Endpoints CRUD | Status |
|---|---|---|
| auth | register, login, /users/me, refresh | ✅ Completo |
| users | /users/me | ✅ |
| accounts | GET/POST/PATCH/DELETE | ✅ Backend ok |
| categories | CRUD + seed | ✅ |
| transactions | CRUD + filtros + paginação | ✅ |
| budgets | CRUD + cálculo de uso | ✅ |
| goals | CRUD + status automático | ✅ |
| dashboard | summary, breakdown, cashflow | ✅ |
| imports/csv | upload + parser + categorização | ✅ |
| core | rate limiting, errors, logging | ✅ |

### Mobile (Flutter)

| Tela | Status | Observação |
|---|---|---|
| Splash | ✅ | |
| Login / Cadastro | ✅ | |
| Dashboard | ✅ | Sem botão "+ Nova transação" |
| Lista de Transações | ✅ | FAB "+" presente, filtros via bottom-sheet |
| Nova / Editar Transação | ✅ | Formulário completo com tipo, valor, conta, categoria, data, forma de pagamento, recorrente |
| Categorias (lista + form) | ✅ | |
| Orçamentos (lista + form) | ✅ | |
| Metas (lista + form) | ✅ | |
| Perfil | ✅ | |
| Configurações | ✅ | |
| Importar CSV | ✅ | |
| **Contas (accounts)** | ❌ **AUSENTE** | Não existe `lib/features/accounts/`. Drawer não tem item "Contas". |

### Painel Web (Streamlit)

| Página | Status | Observação |
|---|---|---|
| Login | ✅ | |
| Home logada | ✅ | Apenas dados do usuário |
| 1_Relatorios | ✅ | Visualização |
| 2_Transacoes | ✅ | **Apenas listar + filtrar + exportar CSV/XLSX. Sem criar, editar ou excluir.** |
| Cadastro de transação | ❌ | Lacuna identificada pelo usuário |
| Importar CSV | ❌ | Existe no backend e no mobile, mas não no painel |
| Gestão de contas | ❌ | |
| Gestão de categorias | ❌ | |
| Gestão de orçamentos / metas | ❌ | |

### Planejamento (planilha)

- 17 sprints, 67 tarefas, 46 itens de teste.
- A tarefa **S03 (CRUD de Contas)** cobriu só o backend. **Nunca foi planejada uma tela de Contas no mobile nem no painel.** Isso explica por que ela não existe — não foi um erro de execução, foi um buraco no plano original.

---

## 3. Análise — Product Owner

### O que está bem
- MVP coerente: receitas, despesas, contas, categorias, orçamentos, metas, dashboard, import CSV.
- Roadmap por fases com prioridades claras (backend → mobile → painel → deploy).
- Política anti-regressão e estratégia de testes embutidas no plano.

### Problemas
1. **Furo no escopo do MVP: gestão de contas.** O blueprint lista "Gestão de Contas" como funcionalidade 2 do MVP, mas só virou tarefa de backend. Sem tela, o usuário não consegue completar a jornada mínima.
2. **Painel web sub-especificado.** O painel foi pensado como "relatórios", mas é a interface mais cômoda em desktop para entrada de dados (digitar várias transações de uma vez). Hoje ele é só de leitura.
3. **Critério "completude do MVP" frouxo.** Não há, no plano, um checklist objetivo de "fluxo do usuário ponta-a-ponta funciona". Cada tarefa tem critério próprio, mas nada amarra a jornada.
4. **Personas e cenários de uso ausentes.** O blueprint descreve features, não usuários. Sem persona definida, decisões de UX são feitas em vácuo.
5. **Métricas de sucesso do produto não definidas.** O que conta como sucesso? Retenção? Frequência de lançamentos? Tempo médio para registrar uma despesa? Nada disso está escrito.

### Recomendações PO (para a próxima fase)
- **R1 (crítico):** Adicionar como tarefa explícita "Tela de Gestão de Contas (mobile + painel)".
- **R2 (alta):** Estender o painel web para CRUD de transação, gestão de contas e import CSV — replicando o backend que já existe.
- **R3 (média):** Definir 2 personas básicas (ex.: "Marina, faz tudo pelo celular" / "Carlos, prefere planilha no notebook") e mapear a jornada de cada uma.
- **R4 (média):** Definir critério objetivo de "MVP pronto": novo usuário consegue, em ≤ 3 minutos, registrar conta inicial, lançar 5 transações, ver o dashboard refletindo, sem sair do app.
- **R5 (baixa):** Definir 3 métricas-norte (ex.: tempo médio para lançar transação, % usuários com ≥ 1 orçamento, retenção D7).

---

## 4. Análise — UX Designer

### O que está bem
- Navegação via drawer cobre todas as seções principais.
- Estados de loading, vazio e erro implementados nas listas (transações, dashboard).
- Pull-to-refresh em telas-chave.
- Confirmação antes de excluir transação.
- Formulário de transação com validação clara (valor, conta, categoria obrigatórios).

### Problemas e fricções
1. **Tarefa primária (lançar transação) tem 3 passos no mobile.** Dashboard → ícone Transações → FAB "+". Padrão de mercado: FAB "+" direto no Dashboard ou bottom-nav com aba "+" central.
2. **Sem acesso a Contas em lugar nenhum.** Drawer mobile não tem "Contas". Painel não tem. Bloqueio absoluto para usuário novo.
3. **Não há onboarding após cadastro.** Usuário recém-registrado cai no Dashboard vazio, sem orientação sobre "crie sua primeira conta", "crie sua primeira transação", etc.
4. **Não há feedback de saldo após lançar transação.** O snackbar mostra "Transação criada", mas o usuário não vê imediatamente o impacto no saldo da conta.
5. **Drawer mistura ações de produto e configurações.** Importar CSV e Configurações estão no mesmo nível de Dashboard e Transações. Ideal seria agrupar em "Ações" e "Conta do usuário".
6. **Filtros aplicados na lista de transações não persistem entre sessões.** Toda vez que abre, volta ao default. Em uso real, o usuário tende a olhar sempre o mês corrente — isso já é o default, mas seria útil lembrar a última seleção.
7. **Painel Streamlit não autentica entre páginas com elegância.** Cada página chama `st.session_state["token"]` e dá `st.stop()` se faltar. Funciona, mas ao redirecionar perdeu o contexto da página tentada.
8. **Sem busca/atalho global.** Em apps financeiros, "ver as últimas X transações da categoria Y" é uma busca comum. Hoje requer filtros manuais.
9. **Acessibilidade não foi tratada explicitamente.** Não vi labels semânticos, contraste de cores validado, tamanhos de fonte ajustáveis nem `Semantics` no Flutter.
10. **Drawer sem destaque do item ativo.** Em apps grandes isso confunde — usuário não sabe onde está.

### Jornadas críticas com atrito alto

**Jornada 1 — primeiro uso (novo usuário)**
1. Cadastra-se ✅
2. Tenta lançar uma despesa → cai no formulário → seletor de Conta vazio → trava ❌
3. **Não encontra como criar conta** ❌

**Jornada 2 — uso diário (lançar gasto rápido)**
1. Abre app → Dashboard
2. Quer lançar R$ 25 de almoço
3. Toca "Transações" no header (1 toque)
4. Toca FAB "+" (2 toques)
5. Preenche valor, conta, categoria, data → Salva (4 toques + digitação)
6. Volta para a lista — não vê o saldo da conta atualizado sem voltar ao Dashboard

→ Alvo: 3 toques + digitação no caminho ideal.

**Jornada 3 — fechamento mensal pelo computador (uso painel)**
1. Abre painel → faz login
2. Vai em Transações → vê lista filtrável ✅
3. Quer corrigir uma transação digitada errada → **não pode editar pelo painel** ❌
4. Quer adicionar uma transação que esqueceu → **não pode cadastrar pelo painel** ❌
5. Tem que abrir o celular para corrigir.

### Recomendações UX (para a próxima fase)
- **R6 (crítico):** Adicionar item "Contas" no drawer mobile + criar telas equivalentes às de Categorias (lista + formulário).
- **R7 (alta):** FAB "+" direto no Dashboard mobile, abrindo a tela de Nova Transação.
- **R8 (alta):** Onboarding mínimo após cadastro: 1 tela "Crie sua primeira conta" obrigatória → 1 tela opcional "Lance sua primeira transação".
- **R9 (alta):** Estender painel web com formulários de cadastro/edição de transação e CRUD de conta.
- **R10 (média):** Após salvar transação, exibir o novo saldo da conta no snackbar de sucesso.
- **R11 (média):** Highlight do item ativo no drawer (Material 3 `NavigationDrawer`).
- **R12 (média):** Reorganizar drawer em seções: "Lançamentos" (Dashboard, Transações, Contas, Categorias, Orçamentos, Metas, Importar CSV) e "Conta" (Perfil, Configurações, Sair).
- **R13 (baixa):** Persistir últimos filtros aplicados na lista de transações (SharedPreferences).
- **R14 (baixa):** Auditoria de acessibilidade — labels semânticos, contraste, tamanho mínimo de toque (48dp), suporte a screen reader.

---

## 5. Análise — UI Designer

### O que está bem
- Uso consistente de Material 3 no mobile (Card, FilledButton, OutlinedButton, SegmentedButton).
- Cores semânticas para receita (verde) e despesa (vermelho).
- Tipografia herda do tema (`textTheme.titleMedium`, `bodyLarge`, etc.) — bom para acessibilidade.
- Ícones consistentes (Material Icons).

### Problemas
1. **Não há Design System documentado.** Não existe arquivo de tokens (cores, espaçamentos, tipografia, raios), nem Figma referenciado. Cada tela define cores inline (`Colors.green`, `Colors.red`, `Colors.blueGrey`).
2. **Cores hardcoded em vez de roles do tema.** Ex.: `Colors.green` no _SummaryCard de receita não respeita Material Dynamic Color nem tema escuro.
3. **Sem tema escuro implementado.** A tela de Configurações tem o estado, mas o tema não muda visualmente nas telas (a Sprint 14 marca isso como "Baixa" — mas Dark Mode é hoje uma expectativa básica).
4. **Sem logo, splash visual nem identidade.** A Splash é provavelmente texto + spinner. Logo do "OrçaFácil" não existe.
5. **Painel Streamlit usa padrão visual do Streamlit puro.** Sem customização de tema, sem logo, sem hierarquia visual cuidada.
6. **Charts (fl_chart) sem rótulos de eixo nem unidades.** O gráfico de pizza no Dashboard mostra %, mas a legenda lateral mostra apenas nome (Valor). Falta uma indicação clara da unidade (R$).
7. **Tabelas e listas sem zebra striping nem hover.** Padrão visual cansativo em uso prolongado (especialmente no painel).
8. **Cores de orçamento (verde/amarelo/vermelho) sem padronização entre módulos.** Cada controller pode estar definindo seu próprio matiz.
9. **Sem ícones por categoria no app.** O backend tem campo `icon`, mas não vi a tela de criar categoria oferecer um picker — provavelmente é input de texto livre.

### Recomendações UI (para a próxima fase)
- **R15 (alta):** Criar `lib/core/theme/` com `app_colors.dart`, `app_text_styles.dart`, `app_spacing.dart` — substituir cores hardcoded por referências do tema.
- **R16 (alta):** Implementar tema escuro de fato (não só o toggle).
- **R17 (média):** Definir logo simples (texto estilizado serve para MVP) + splash com identidade.
- **R18 (média):** Customizar tema do Streamlit (`.streamlit/config.toml`) com paleta consistente com o app.
- **R19 (média):** Adicionar picker de ícone na tela de categorias (lista fixa de ~20 Material Icons).
- **R20 (baixa):** Padronizar paleta de status (sucesso/aviso/erro) num único arquivo.

---

## 6. Resposta direta ao seu questionamento

> "Não localizei tela pra cadastrar entradas/saídas, acho que está somente por CSV, quero a opção de cadastro também, avalia se é possível inserir isso nessa próxima fase."

**Resposta curta:** existe no app **mobile**, não existe no **painel web**. Sua percepção bate com a realidade se você esteve olhando o painel.

**Detalhamento:**

| Onde | Cadastro manual de transação? |
|---|---|
| Backend (API) | ✅ POST /transactions completo |
| Mobile Flutter | ✅ Tela `transaction_form_screen.dart` (criar/editar/excluir) |
| Painel Streamlit | ❌ **Não existe** — só lista, filtra e exporta |
| Importação CSV (mobile) | ✅ Funciona |
| Importação CSV (painel) | ❌ Não existe (só no mobile) |

**É viável adicionar na próxima fase?** Sim, perfeitamente. O backend já tem tudo. Para o painel, é construir uma página Streamlit nova (`pages/3_Nova_Transacao.py`) consumindo os mesmos endpoints. Esforço estimado: 1 sprint contemplando:
- Cadastro/edição/exclusão de transação no painel
- Cadastro/edição/exclusão de conta no painel
- Importação CSV no painel
- Uma melhoria de UX no mobile (FAB no Dashboard) para fechar a lacuna do "primeiro toque".

Proposta de sprint dedicada a isso na seção 8.

---

## 7. Problemas críticos consolidados (priorizados)

| # | Severidade | Problema | Onde | Impacto |
|---|---|---|---|---|
| P1 | 🔴 Crítica | Não há tela de gestão de Contas | Mobile + Painel | Bloqueia novo usuário (sem conta, não dá pra lançar transação) |
| P2 | 🔴 Crítica | Painel não permite cadastrar transação | Painel | Usuário desktop precisa abrir celular para tudo |
| P3 | 🟠 Alta | Sem atalho de "Nova transação" no Dashboard | Mobile | 3 toques para a ação mais frequente |
| P4 | 🟠 Alta | Sem onboarding pós-cadastro | Mobile + Painel | Usuário novo se perde |
| P5 | 🟠 Alta | Importação CSV ausente no painel | Painel | Capacidade existente não exposta na interface ideal |
| P6 | 🟡 Média | Sem Design System / tokens | Mobile + Painel | Inconsistência visual, débito acumulando |
| P7 | 🟡 Média | Sem tema escuro real | Mobile | Expectativa de mercado não atendida |
| P8 | 🟡 Média | Drawer sem item ativo destacado | Mobile | UX confusa |
| P9 | 🟡 Média | Sem feedback de saldo pós-transação | Mobile | Usuário não vê o efeito do que digitou |
| P10 | 🟢 Baixa | Filtros não persistem entre sessões | Mobile | Atrito leve |
| P11 | 🟢 Baixa | Acessibilidade não auditada | Mobile + Painel | Risco de inclusão / loja de aplicativos |
| P12 | 🟢 Baixa | Charts sem unidade explícita (R$) | Mobile | Risco de leitura ambígua |

---

## 8. Sprints propostas — Próxima fase

Sugestão de estrutura, sem mexer no que já está concluído. Cada sprint segue o mesmo formato do planejamento existente (ID, descrição, critérios, dependências, testes obrigatórios). A versão definitiva entra na planilha [orcafacil_planejamento_sprints.xlsx](orcafacil_planejamento_sprints.xlsx) como Sprints 17, 18 e 19.

### Sprint 17 — Gestão de Contas (mobile + painel) — fechar P1

- S17-T01 — `categories/icons`: catálogo fixo de ícones reutilizável (também serve à Sprint 19).
- S17-T02 — Tela "Contas" no mobile: lista, filtro por ativa/inativa, FAB "+", deletar com confirmação.
- S17-T03 — Tela "Formulário de Conta" no mobile (criar/editar): nome, tipo, saldo inicial, ativa.
- S17-T04 — Item "Contas" no drawer + reorganização das seções do drawer.
- S17-T05 — Página "Contas" no painel Streamlit (CRUD).
- **Testes obrigatórios:** widget tests do formulário (3 estados); integration test mobile criando conta → lançando transação → vendo saldo correto; teste do painel com mock da API; teste de regressão de saldo permanece verde.

### Sprint 18 — Painel Web com cadastro completo — fechar P2 e P5

- S18-T01 — Página "Nova Transação" no painel (form + select de conta/categoria + submit).
- S18-T02 — Edição inline na lista de Transações do painel (clique → modal de edição).
- S18-T03 — Exclusão com confirmação na lista do painel.
- S18-T04 — Página "Importar CSV" no painel (file uploader + resumo de sucesso/erro).
- S18-T05 — Customização visual do painel (`.streamlit/config.toml`, logo simples, tipografia).
- **Testes obrigatórios:** teste de API consumida pelo painel; smoke test E2E (login → cadastrar conta → cadastrar transação → ver no relatório); regressão de saldo continua verde.

### Sprint 19 — Polimento UX/UI — fechar P3, P4, P6, P7, P8, P9

- S19-T01 — FAB "+" no Dashboard mobile + roteamento direto para Nova Transação.
- S19-T02 — Onboarding pós-cadastro: 1 modal obrigatório "Crie sua primeira conta", 1 dica opcional após.
- S19-T03 — `lib/core/theme/` com tokens (cores, tipografia, espaçamentos) + refatoração de cores hardcoded.
- S19-T04 — Tema escuro funcional (não só toggle): paleta, contraste validado, screenshots dos 2 modos.
- S19-T05 — Item ativo destacado no drawer.
- S19-T06 — Snackbar pós-transação mostrando novo saldo da conta.
- S19-T07 — Picker de ícone na tela de Categorias.
- **Testes obrigatórios:** widget tests do onboarding, do FAB do Dashboard e do drawer com item ativo; teste de tema (snapshot/golden) nos dois modos.

### Backlog futuro (não bloqueante)

- Persistência de filtros (P10).
- Auditoria de acessibilidade (P11).
- Charts com unidades e tooltip (P12).
- Personas, métricas-norte e critério "MVP pronto" objetivo (decisões PO).
- Multiusuário familiar (item já listado em "Possíveis Melhorias Futuras" do blueprint).

---

## 9. Riscos e considerações de implementação

- **Regressão de saldo é o risco principal.** Toda mudança nas Sprints 17-19 que toca cadastro de transação ou conta precisa rodar a bateria `test_balance_regression_full_flow` antes de marcar tarefa como concluída.
- **Painel e mobile devem ficar funcionalmente equivalentes.** Para evitar divergência de comportamento, documentar na aba "Decisões" da planilha a regra "cadastro de transação deve produzir o mesmo efeito em qualquer interface".
- **Onboarding não pode virar barreira.** A tela "Crie sua primeira conta" precisa ter atalho "Pular por agora" para não bloquear quem só quer explorar — mas com aviso de que sem conta não dá pra lançar transação.
- **Design tokens precisam vir antes do tema escuro.** Se o tema escuro for feito antes da refatoração de cores hardcoded, vamos refazer trabalho. Ordem: S19-T03 → S19-T04.
- **Não inflar escopo das Sprints 17-19 com features novas do backend.** Tudo o que está aqui usa APIs que já existem. Se aparecer ideia de mudança no backend (ex.: transferência entre contas), vira sprint própria, não entra aqui.

---

## 10. Próximos passos sugeridos

1. Você (Product Owner) lê esta análise e valida quais itens entram na próxima fase.
2. Eu (ou outro chat) atualizo a planilha `orcafacil_planejamento_sprints.xlsx` adicionando Sprints 17, 18, 19 conforme os itens aprovados, mantendo o mesmo padrão das anteriores (descrição, critérios, dependências, testes obrigatórios).
3. Atualizo o `PROMPT_INICIAL_CLAUDE_CODE.md` para o chat executor pegar o trabalho de onde parou e seguir nas novas sprints sem ambiguidade.
4. Decisões tomadas vão para a aba "Decisões" da planilha — incluindo justificativa de cada item priorizado/despriorizado.

Me confirme quais recomendações entram, quais ficam para depois e quais você descarta — daí monto as sprints novas na planilha.
