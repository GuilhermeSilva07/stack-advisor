# Plano de Aula 2 – Agente Scout

## Objetivo
Desenvolver o agente **Scout**, responsável por buscar comparações reais entre as tecnologias recomendadas no perfil do projeto (`data/project-profile.md`). O agente deve:

1. Utilizar o **Firecrawl** (já instalado e disponível como CLI) para obter comparações, prós, contras e casos de uso reais de cada tecnologia da stack.
2. Caso o Firecrawl falhe, recorrer à **ferramenta de acesso web nativa do Zed** (search/scrape) como fallback.
3. Estruturar a saída em listas numeradas com pares chave‑valor, conforme a convenção do projeto.
4. Registrar eventuais erros no campo `erros` e abortar a tarefa caso não seja possível obter dados.

## Fluxo de Execução do Scout

1. **Leitura do perfil do projeto** – ler `data/project-profile.md` para obter a lista de tecnologias a comparar (frontend, backend, banco, deploy, extras).
2. **Construção das queries** – para cada tecnologia gerar uma query de busca que solicite comparações reais, prós/contras e exemplos de uso em produção.
3. **Execução via Firecrawl** – chamar a CLI do Firecrawl (`firecrawl scrape <url> --search "<query>"`) ou usar o comando adequado (`firecrawl search`).
4. **Fallback** – se o comando retornar erro ou saída vazia, usar a ferramenta de web nativa do Zed (`search`/`fetch`) para obter conteúdo da mesma query.
5. **Parseamento da resposta** – extrair as comparações relevantes e formatar em:
   ```markdown
   ## Comparações de <Tecnologia>
   1. **Tecnologia A** – pros, contras, caso de uso real
   2. **Tecnologia B** – ...
   ```
6. **Agregação** – combinar todas as comparações em um único documento `data/scout-results.md`.
7. **Registro de erros** – caso algum passo falhe, incluir no campo `erros` do documento e parar a execução.

## Estrutura de Arquivos
```
stack-advisor/
├── data/
│   ├── project-profile.md   # já existente – contém a stack recomendada
│   └── scout-results.md     # será criado pelo agente Scout
├── plano-aula-2.md          # este plano de aula
└── ...
```

## Detalhamento das Tarefas

### Tarefa 1 – Preparar ambiente
- Verificar se o binário `firecrawl` está no `PATH` (comando `firecrawl --version`).
- Caso não esteja, registrar erro e usar fallback imediatamente.

### Tarefa 2 – Ler stack recomendada
- **Ferramenta:** `read_file` → `data/project-profile.md`.
- Extrair as seções `Frontend`, `Backend`, `Banco de dados`, `Deploy` e `Extras`.

### Tarefa 3 – Gerar queries de busca
Para cada tecnologia gerar uma query do tipo:
```
Comparação real entre <Tecnologia> e alternativas, incluindo prós, contras e casos de uso em produção (ex.: empresas que usam).
```
Exemplo para React:
```
Comparação real entre React e Vue, com prós, contras e exemplos de empresas que utilizam React em produção.
```

### Tarefa 4 – Executar busca com Firecrawl
- **Comando:** `firecrawl search "<query>" --format markdown`
- Capturar a saída. Se o retorno contiver a palavra `error` ou estiver vazio, considerar falha.

### Tarefa 5 – Fallback para ferramenta web nativa
- Utilizar a skill **firecrawl-search** (ou a ferramenta `search` do Zed) com a mesma query.
- Processar o markdown retornado da mesma forma.

### Tarefa 6 – Processar resultados
- Remover trechos irrelevantes (publicidade, licenças).
- Manter apenas listas numeradas com pares chave‑valor:
  ```
  1. **React** – Pro: comunidade grande, ecossistema rico; Contra: bundle size; Caso real: Facebook, Instagram.
  ```
- Repetir para todas as tecnologias.

### Tarefa 7 – Persistir saída
- **Ferramenta:** `write_file` → `data/scout-results.md`.
- Estrutura final do arquivo:
  ```markdown
  # Resultados do Scout

  ## Comparações de Frontend
  1. **React** – ...
  2. **Vue** – ...

  ## Comparações de Backend
  1. **Node.js (Express)** – ...
  2. **NestJS** – ...

  ## Comparações de Banco de Dados
  ...

  ## Comparações de Deploy
  ...

  ## Erros
  - <lista de erros, se houver>
  ```

### Tarefa 8 – Comunicação de conclusão
- O agente deve responder ao orquestrador com um envelope de resposta:
  ```markdown
  ## RESPOSTA: Scout
  ### estado
  success
  ### resumo
  Comparações reais obtidas e armazenadas em `data/scout-results.md`.
  ### dados
  1. caminho: data/scout-results.md
  ### erros
  (vazio se nenhum)
  ```

## Requisitos Técnicos
- **Linguagem:** O agente não deve gerar código; ele age apenas via comandos CLI e manipulação de arquivos Markdown.
- **Idiomas:** Respostas em português brasileiro, termos técnicos em inglês.
- **Ferramentas permitidas:** `spawn_agent`, `terminal`, `read_file`, `write_file`, `edit_file`, `find_path`.
- **Fallback:** Nunca deixar a tarefa incompleta sem relatar o motivo.

## Critérios de Aceite
- `data/scout-results.md` é criado e contém comparações estruturadas para todas as tecnologias listadas em `project-profile.md`.
- Não há mensagens de erro não tratadas; quaisquer falhas são registradas no campo `erros`.
- O agente utiliza Firecrawl quando possível; caso contrário, usa a ferramenta web nativa.
- O formato de saída segue a convenção de listas numeradas sem tabelas.

---

*Este plano de aula complementa o `plano.md` original, detalhando a implementação do agente Scout.*