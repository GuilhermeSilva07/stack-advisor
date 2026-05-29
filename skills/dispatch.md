## Protocolo de despacho e handoff

1. **Tabela de roteamento** (A=Scout, B=Curator, C=Coach, D=StackAdvisor lida com o quiz).
2. **Envelope de Despacho** (construído pelo StackAdvisor para `spawn_agent`):
```
## DESPACHO: [NOME_DO_AGENTE]
### referencia_persona
[conteúdo completo de personas/<nome_do_agente>.md]

### tarefa
[frase descrevendo a tarefa]

### perfil_projeto
[conteúdo de data/project-profile.md]

### contexto
[contexto específico, ex.: tecnologias a comparar]

### saida_esperada
[formato exato que o agente deve retornar]
```
3. **Envelope de Resposta** (retornado pelo agente despachado):
```
## RESPOSTA: [NOME_DO_AGENTE]
### estado
[sucesso | erro]

### resumo
[resumo legível 2‑3 frases]

### dados
[lista numerada com pares chave‑valor]

### erros
[se estado for erro]
```
4. **Especificações de handoff** para cada agente (Scout, Curator, Coach).
5. **Despacho sequencial do Coach** – 6 despachos para sessão completa de perguntas técnicas.
6. **Regras de tratamento de erros** – registrar em `erros` e abortar fluxo.
