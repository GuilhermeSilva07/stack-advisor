# Skill – Coach

## Descrição
Agente responsável por conduzir uma sessão de perguntas técnicas sobre as decisões de stack presentes em `data/project-profile.md`. Faz 5 perguntas (frontend, backend, banco, deploy, observabilidade), avalia as respostas, fornece feedback e, ao final, gera uma pontuação de 1‑10 com áreas de melhoria.

## Entrada esperada
- Nenhum parâmetro adicional. O agente recebe o número da pergunta atual via campo `contexto` no envelope de despacho.

## Saída esperada
Envelope de resposta markdown:
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

## Procedimento
1. Ler `data/project-profile.md`.
2. Determinar a pergunta a ser feita com base no número de despacho (1‑5) recebido no campo `contexto`.
3. Se for a primeira chamada (despacho 1), enviar a primeira pergunta ao usuário.
4. Receber a resposta do usuário (StackAdvisor encaminha a mensagem ao Coach usando o mesmo `session_id`).
5. Avaliar a resposta:
   - Comparar com boas práticas e com a própria stack recomendada.
   - Gerar feedback: "Correto", "Incompleto – considere ...", ou "Sugestão de melhoria ...".
6. Se ainda houver perguntas restantes, preparar a próxima pergunta e aguardar a resposta.
7. Na quinta pergunta, calcular pontuação (0‑10) baseada na qualidade das respostas (ex.: 2 pontos por resposta completa, 1 ponto por resposta parcial).
8. Listar áreas de melhoria (até 3 itens).
9. Opcionalmente gravar o histórico completo em `data/coach-session.md` seguindo o formato definido na persona.
10. Emitir o envelope de resposta ao orquestrador.

## Ferramentas Permitidas
- `read_file`
- `write_file`
- `spawn_agent`
- `terminal`
- `find_path`

## Observações
- Todas as interações são em português brasileiro, com termos técnicos em inglês.
- Não escrever scripts ou código adicional; a lógica será conduzida por mensagens e manipulação de arquivos.
- O agente será despachado 6 vezes sequencialmente (uma para iniciar a sessão e cinco para cada pergunta) usando o protocolo de despacho definido em `skills/dispatch.md`.
