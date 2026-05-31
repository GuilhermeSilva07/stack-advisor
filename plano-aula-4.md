# Plano de Aula 4 – Agente Coach

## Objetivo
Desenvolver o agente **Coach**, responsável por simular uma sessão de perguntas técnicas sobre as decisões de stack presentes em `data/project-profile.md`. O Coach deve:

1. Fazer **5 perguntas técnicas**, uma por vez, relacionadas à stack recomendada (frontend, backend, banco, deploy, observabilidade/extras).
2. Receber a resposta do usuário, fornecer **feedback imediato** (correto, incompleto, ou sugestão de melhoria).
3. Após a quinta pergunta, apresentar uma **pontuação final de 1 a 10** e listar **áreas de melhoria**.
4. Ser despachado **6 vezes sequencialmente** pelo StackAdvisor (uma para iniciar a sessão e cinco para cada pergunta).

## Fluxo de Execução do Coach

1. **Leitura do perfil do projeto** – ler `data/project-profile.md` para extrair a stack recomendada.
2. **Geração das perguntas** – criar 5 perguntas técnicas, cada uma focando em um aspecto da stack (ex.: escolha do frontend, padrão de API, modelagem do banco, estratégia de deploy, observabilidade). As perguntas devem ser formuladas em português, com termos técnicos em inglês quando necessário.
3. **Desdobramento sequencial** – o StackAdvisor despacha o Coach 6 vezes usando o protocolo de despacho (`skills/dispatch.md`). Cada despacho contém:
   - `referencia_persona` → conteúdo completo de `personas/coach.md` (a ser criado).
   - `tarefa` → “Fazer a próxima pergunta técnica e avaliar a resposta”.
   - `perfil_projeto` → conteúdo de `data/project-profile.md`.
   - `contexto` → número da pergunta atual (1‑5) e a pergunta em si.
   - `saida_esperada` → envelope de resposta com `estado`, `resumo`, `dados` (pergunta feita, feedback) e `erros`.
4. **Interação** – a cada despacho o Coach:
   - Exibe a pergunta ao usuário.
   - Aguarda a resposta (texto livre).
   - Avalia a resposta (comparando com boas práticas e a própria stack) e devolve feedback.
5. **Pontuação final** – após a quinta pergunta, o Coach calcula uma nota de 1‑10 baseada na qualidade das respostas e gera um resumo de áreas de melhoria.
6. **Persistência (opcional)** – pode escrever um resumo da sessão em `data/coach-session.md` para histórico.

## Estrutura de Arquivos
```
stack-advisor/
├── data/
│   ├── project-profile.md   # já existente – contém a stack recomendada
│   └── coach-session.md     # (opcional) histórico da sessão de perguntas
├── personas/
│   └── coach.md             # persona do Coach (a ser criada)
├── skills/
│   └── coach.md             # skill que descreve o procedimento do Coach
├── plano-aula-4.md          # este plano de aula
└── ...
```

## Detalhamento das Tarefas

### Tarefa 1 – Criar a Persona do Coach
- **Arquivo:** `personas/coach.md`
- Descrever responsabilidade, fluxo de inicialização (ler profile, receber pergunta, dar feedback, pontuar), ferramentas permitidas (`read_file`, `write_file`, `spawn_agent`, `terminal`, `find_path`) e formato de saída (`data/coach-session.md`).

### Tarefa 2 – Criar a Skill do Coach
- **Arquivo:** `skills/coach.md`
- Documentar passo‑a‑passo:
  1. Ler `data/project-profile.md`.
  2. Determinar a pergunta a ser feita com base no número de despacho (recebido via `contexto`).
  3. Esperar resposta do usuário (o StackAdvisor encaminha a mensagem ao Coach). 
  4. Avaliar a resposta e gerar feedback.
  5. Na última chamada (pergunta 5) calcular pontuação e listar áreas de melhoria.
  6. Opcionalmente gravar resumo em `data/coach-session.md`.
- Definir o envelope de resposta esperado:
  ```markdown
  ## RESPOSTA: Coach
  ### estado
  success | error
  ### resumo
  <texto curto da pergunta ou da pontuação final>
  ### dados
  1. pergunta: <texto da pergunta>
  2. resposta_usuario: <texto recebido>
  3. feedback: <texto de avaliação>
  ### erros
  - <lista de erros, se houver>
  ```

### Tarefa 3 – Definir as 5 Perguntas Técnicas
| Nº | Pergunta (exemplo) |
|---|---|
| 1 | **Frontend** – Por que escolheu **Next.js** ao invés de React puro? Quais benefícios específicos para o projeto? |
| 2 | **Backend** – Como o **Node.js + Express** será estruturado para suportar a escalabilidade futura? |
| 3 | **Banco de Dados** – Qual estratégia de modelagem você adotará no **PostgreSQL (Supabase)** para garantir integridade e performance? |
| 4 | **Deploy** – Como o **Vercel** e **Railway** serão configurados para CI/CD e preview deployments? |
| 5 | **Observabilidade** – Como pretende usar **LogRocket** ou **Sentry** para monitoramento e debugging? |

### Tarefa 4 – Implementar Despacho Sequencial no StackAdvisor
- O StackAdvisor, ao receber a escolha **C** (simular perguntas técnicas), deve iniciar a sequência:
  1. **Despacho 1** – iniciar sessão (Coach envia a primeira pergunta).
  2. **Despacho 2‑6** – após cada resposta do usuário, o StackAdvisor repassa a mensagem ao Coach (mesmo `session_id`) para que ele faça a próxima pergunta e dê feedback.
- Cada despacho usa o mesmo `session_id` para manter o estado da sessão.

### Tarefa 5 – Persistir Histórico (opcional)
- **Ferramenta:** `write_file` → `data/coach-session.md`.
- Estrutura sugerida:
  ```markdown
  # Sessão Coach – Simulação de Perguntas Técnicas

  ## Pergunta 1
  - **Pergunta:** ...
  - **Resposta:** ...
  - **Feedback:** ...

  ## Pergunta 2
  ...

  ## Pontuação Final
  - **Nota:** X/10
  - **Áreas de melhoria:**
    1. ...
    2. ...
  ```

## Requisitos Técnicos
- **Linguagem:** O agente não gera código; opera via comandos CLI e manipulação de arquivos Markdown.
- **Idiomas:** Respostas em português brasileiro, termos técnicos em inglês.
- **Ferramentas Permitidas:** `spawn_agent`, `read_file`, `write_file`, `edit_file`, `find_path`.
- **Sequenciamento:** O Coach será despachado **6 vezes sequencialmente**; não paralelamente.

## Critérios de Aceite
1. **Persona** (`personas/coach.md`) e **skill** (`skills/coach.md`) criados conforme especificado.
2. **5 perguntas técnicas** definidas e prontas para serem enviadas em sequência.
3. **Feedback** gerado após cada resposta, com avaliação clara.
4. **Pontuação final** (1‑10) e lista de **áreas de melhoria** apresentadas na última resposta.
5. **Histórico** opcional salvo em `data/coach-session.md`.
6. **Desdobramento** funciona com `spawn_agent` usando o protocolo de despacho (`skills/dispatch.md`).
7. Formato de saída segue o envelope descrito (sem tabelas, usando listas numeradas).

---

*Este plano de aula complementa os planos anteriores (`plano.md`, `plano-aula-2.md`, `plano-aula-3.md`) e detalha a implementação do agente Coach, responsável por conduzir a simulação de perguntas técnicas e avaliação de respostas.*