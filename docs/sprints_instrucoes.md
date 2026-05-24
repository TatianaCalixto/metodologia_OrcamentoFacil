# Instruções

> Renderizado a partir de `orcafacil_planejamento_sprints.xlsx` (aba **Instruções**).

OrçaFácil — Documentação Verdade das Sprints
Esta planilha é a fonte única de verdade para a execução do projeto. O chat do Claude Code que vai trabalhar nas tarefas deve usá-la como roteiro, registrar evolução nela e nunca tomar decisões de escopo fora dela.

Como o Claude Code deve usar esta planilha
1. Sempre comece a sessão lendo a aba 'Visão Geral' para entender o estado macro.
2. Em seguida, abra a aba 'Backlog' e localize a próxima tarefa com status 'Pendente' respeitando a coluna 'Dependências' (não pular ordem).
3. Marque a tarefa como 'Em andamento' e preencha 'Data Início'.
4. Implemente a tarefa seguindo exatamente o que está em 'Descrição' e 'Critérios de Aceitação'. Não adicionar features extras.
5. Escrever os testes listados em 'Testes Obrigatórios' ANTES de marcar como concluída. Sem testes verdes a tarefa não é concluída.
6. Rodar a suíte completa de testes do módulo afetado para garantir que nada regrediu antes de concluir.
7. Mudar status para 'Em teste' quando o código estiver pronto e os testes estiverem rodando; mudar para 'Concluída' quando tudo passar.
8. Preencher 'Data Fim' e qualquer 'Observação' relevante (decisões tomadas, arquivos criados, comandos úteis para rodar).
9. Se houver dúvida que mude o escopo ou comportamento esperado da tarefa, marcar 'Impedimento? = Sim', escrever a dúvida e PARAR. Perguntar para o humano via mensagem. Nunca decidir sozinho.

Regras inegociáveis
• Toda tarefa que toca regra de negócio (saldo, alerta de orçamento, status de meta, import CSV) DEVE ter testes que travam a regressão.
• A bateria de regressão de saldo (S05-T05) deve continuar verde em todas as sprints seguintes. Se quebrar, parar e investigar.
• Cobertura de testes do backend deve permanecer ≥ 80% a partir da Sprint 10.
• Nunca commitar segredos. Sempre usar .env e .env.example.
• Nunca apagar dados em produção sem autorização explícita do humano.
• Mudanças de schema sempre via migration Alembic — nunca direto no banco.
• Para qualquer decisão técnica não trivial, registrar na aba 'Decisões'.

Sobre a aba 'Plano de Testes'
Lista de checks de teste que precisam existir ao final do projeto. Marcar como 'Concluída' assim que o teste correspondente estiver no repositório e verde no CI.

Sobre a aba 'Impedimentos'
Registrar QUALQUER bloqueio antes de prosseguir. Cada linha tem ID, sprint/tarefa relacionada, descrição do problema, o que tentou e a pergunta para o humano.

Sobre a aba 'Decisões'
Registrar decisões técnicas não óbvias: por que escolheu biblioteca X, por que estrutura Y, etc. Garante que o próximo Claude entenda o porquê.

Convenções
• IDs de tarefa no formato S##-T## (ex.: S05-T03).
• Status válidos: Pendente, Em andamento, Bloqueada, Em teste, Concluída, Cancelada (dropdown na coluna Status).
• Datas no formato AAAA-MM-DD.
• Observações curtas e úteis, sem narrativa longa.

FASE 2 — Aberta em 2026-05-24
A Fase 1 (Sprints 0 a 16) entregou o MVP do OrçaFácil: backend completo, mobile com a maior parte das telas, painel Streamlit com login e relatórios. Análise pelos papéis Product Owner, UX Designer e UI Designer (documento analise_po_ux_ui_orcafacil.md) identificou lacunas e atrito que motivaram a abertura da Fase 2.
Fase 2 = Sprints 17, 18 e 19 (linhas destacadas em amarelo claro nas abas Visão Geral, Backlog e Plano de Testes).
Sprint 17 — Gestão de Contas (mobile + painel): fecha o bloqueio crítico de não haver tela para criar conta.
Sprint 18 — Painel web com cadastro completo: paridade funcional entre painel e mobile (cadastrar/editar/excluir transação, importar CSV).
Sprint 19 — Polimento UX/UI: atalho de Nova Transação no Dashboard, onboarding pós-cadastro, tokens de tema, dark mode real, item ativo no drawer, snackbar de saldo, picker de ícone.
Regras inegociáveis seguem valendo: testes obrigatórios antes de concluir, regressão de saldo sempre verde, sem decisões fora da planilha. A bateria S05-T05 deve continuar verde após cada sprint da Fase 2.
Justificativa registrada em DEC-001 na aba Decisões.
