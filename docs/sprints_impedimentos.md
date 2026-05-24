# Impedimentos

> Renderizado a partir de `orcafacil_planejamento_sprints.xlsx` (aba **Impedimentos**).

| ID | Data | Sprint | Tarefa Relacionada | Descrição | O que tentou | Pergunta para o humano | Status | Resposta / Decisão |
|---|---|---|---|---|---|---|---|---|
| IMP-001 | 2026-05-24 | 16 | S16-T02 | Provisionamento do banco gerenciado (Supabase) requer criar projeto na nuvem. | Apenas escrevi a config Alembic via DATABASE_URL — nao tenho credencial Supabase para aplicar. | Pode criar o projeto Supabase e me dar a DATABASE_URL (postgresql+psycopg://...)? Vou aplicar as migrations e rodar smoke contra ela. | Resolvido | Usuaria decidiu manter backend local por enquanto (2026-05-24). Tarefa correspondente foi Cancelada. Reabrir quando houver decisao de subir. |
| IMP-002 | 2026-05-24 | 16 | S16-T03 | Deploy do backend em Railway/Render exige conta + secrets na plataforma. | Dockerfile pronto (S16-T01), variaveis documentadas (.env.example). Mas o deploy em si depende de conta + push. | Qual plataforma usar (Railway, Render, Fly.io, outra)? Pode me dar as credenciais ou autorizar deploy a partir do main do GitHub? | Resolvido | Usuaria decidiu manter backend local por enquanto (2026-05-24). Tarefa correspondente foi Cancelada. Reabrir quando houver decisao de subir. |
| IMP-003 | 2026-05-24 | 16 | S16-T04 | Build do APK exige Android SDK + keystore + URL fixa do backend de producao. | Posso preparar o build (flutter build apk --release com --dart-define) mas precisa do APK do agente Flutter e infra. | Quer que eu spawn um agente Flutter para configurar e executar flutter build apk? Voce ja tem um keystore? | Resolvido | Usuaria decidiu manter backend local por enquanto (2026-05-24). Tarefa correspondente foi Cancelada. Reabrir quando houver decisao de subir. |
