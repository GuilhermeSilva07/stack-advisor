# Skill – Scout

## Descrição
Agente responsável por buscar comparações reais entre as tecnologias recomendadas no perfil do projeto (`data/project-profile.md`). Utiliza o CLI do Firecrawl e, em caso de falha, recorre à busca web nativa do Zed.

## Entrada esperada
- Nenhum parâmetro adicional. O agente lê `data/project-profile.md` para obter a lista de tecnologias.

## Saída esperada
Um envelope de resposta markdown:
```markdown
## RESPOSTA: Scout
### estado
success | error
### resumo
<texto resumindo o resultado>
### dados
1. caminho: data/scout-results.md
### erros
- <lista de erros, se houver>
```

## Procedimento
1. Ler `data/project-profile.md`.
2. Extrair as tecnologias (Frontend, Backend, Banco de dados, Deploy, Extras).
3. Para cada tecnologia gerar a query:
   `Comparação real entre <Tecnologia> e alternativas, incluindo prós, contras e casos de uso em produção.`
4. Executar `firecrawl search "<query>" --format markdown` via `terminal` (diretório raiz do projeto).
5. Se o comando falhar ou retornar vazio, usar a skill `firecrawl-search` como fallback.
6. Processar o markdown retornado, mantendo apenas listas numeradas com pares chave‑valor.
7. Consolidar todas as comparações em `data/scout-results.md` seguindo o formato descrito no plano.
8. Registrar quaisquer erros encontrados.
9. Emitir o envelope de resposta.

## Ferramentas Permitidas
- `read_file`
- `write_file`
- `terminal`
- `spawn_agent`
- `find_path`

## Observações
- Todas as respostas devem estar em português brasileiro, com termos técnicos em inglês.
- Não escrever scripts ou código adicional; a lógica é executada por comandos e manipulação de arquivos.
