# Plano de Aula 3 – Agente Curator

## Objetivo
Desenvolver o agente **Curator**, responsável por buscar tutoriais, documentação oficial e cursos das tecnologias recomendadas no perfil do projeto (`data/project-profile.md`). O agente deve:

1. Utilizar o **Firecrawl** (CLI) para encontrar recursos relevantes.
2. Caso o Firecrawl falhe, recorrer à **ferramenta de acesso web nativa do Zed** (search/scrape) como fallback.
3. Produzir uma lista de links úteis, com breve descrição e nível de dificuldade (iniciante, intermediário, avançado).
4. Registrar eventuais erros no campo `erros` e abortar a tarefa caso não consiga obter dados.

## Fluxo de Execução do Curator

1. **Leitura do perfil do projeto** – ler `data/project-profile.md` para obter a lista de tecnologias (frontend, backend, banco, deploy, extras).
2. **Construção das queries** – para cada tecnologia gerar uma query que solicite tutoriais, documentação oficial e cursos, indicando níveis de dificuldade.
3. **Execução via Firecrawl** – usar a CLI (`firecrawl search "<query>" --scrape -o .firecrawl/curator-<tech>.json --json`).
4. **Fallback** – se o comando falhar ou retornar vazio, usar a skill `firecrawl-search` (ou a busca web nativa do Zed) com a mesma query.
5. **Parseamento da resposta** – extrair URLs, títulos, descrições curtas e nível de dificuldade; manter apenas listas numeradas com pares chave‑valor.
6. **Agregação** – combinar todos os recursos em um único documento `data/curator-results.md`.
7. **Registro de erros** – caso algum passo falhe, incluir no campo `erros` do documento e interromper a execução.

## Estrutura de Arquivos
```
stack-advisor/
├── data/
│   ├── project-profile.md   # já existente – contém a stack recomendada
│   └── curator-results.md   # será criado pelo agente Curator
├── plano-aula-3.md          # este plano de aula
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
Tutoriais, documentação oficial e cursos sobre <Tecnologia>, indicando nível de dificuldade (iniciante, intermediário, avançado).
```
Exemplo para Next.js:
```
Tutoriais, documentação oficial e cursos sobre Next.js, indicando nível de dificuldade (iniciante, intermediário, avançado).
```

### Tarefa 4 – Executar busca com Firecrawl
- **Comando:** `firecrawl search "<query>" --scrape -o .firecrawl/curator-<tech>.json --json`
- Capturar a saída JSON. Se o retorno contiver `error` ou estiver vazio, considerar falha.

### Tarefa 5 – Fallback para ferramenta web nativa
- Utilizar a skill **firecrawl-search** (ou a ferramenta `search` do Zed) com a mesma query.
- Processar o JSON ou markdown retornado da mesma forma.

### Tarefa 6 – Processar resultados
- Para cada resultado extrair:
  - **URL**
  - **Título**
  - **Descrição curta** (máx. 2 frases)
  - **Nível** (iniciante / intermediário / avançado) – inferir a partir do título ou descrição quando possível.
- Manter apenas listas numeradas com pares chave‑valor:
  ```
  1. **<Título>** – <Descrição>. Nível: <iniciante|intermediário|avançado>. URL: <link>
  ```
- Repetir para todas as tecnologias.

### Tarefa 7 – Persistir saída
- **Ferramenta:** `write_file` → `data/curator-results.md`.
- Estrutura final do arquivo:
  ```markdown
  # Resultados do Curator

  ## Recursos de Frontend
  1. **Next.js Documentation** – Guia oficial da Vercel; Nível: iniciante. URL: https://nextjs.org/docs
  2. **Next.js Crash Course (YouTube)** – Curso rápido de 30 min; Nível: iniciante. URL: https://youtu.be/...
  ...

  ## Recursos de Backend
  1. **Express.js Guide** – Documentação oficial; Nível: iniciante. URL: https://expressjs.com/
  2. **NestJS Fundamentals (Udemy)** – Curso completo; Nível: intermediário. URL: https://udemy.com/...
  ...

  ## Recursos de Banco de Dados
  ...

  ## Recursos de Deploy
  ...

  ## Erros
  - <lista de erros, se houver>
  ```

### Tarefa 8 – Comunicação de conclusão
- O agente deve responder ao orquestrador com um envelope de resposta:
  ```markdown
  ## RESPOSTA: Curator
  ### estado
  success | error
  ### resumo
  <texto resumindo o resultado>
  ### dados
  1. caminho: data/curator-results.md
  ### erros
  - <lista de erros, se houver>
  ```

## Requisitos Técnicos
- **Linguagem:** O agente não deve gerar código; ele age apenas via comandos CLI e manipulação de arquivos Markdown/JSON.
- **Idiomas:** Respostas em português brasileiro, termos técnicos em inglês.
- **Ferramentas permitidas:** `spawn_agent`, `terminal`, `read_file`, `write_file`, `edit_file`, `find_path`.
- **Fallback:** Nunca deixar a tarefa incompleta sem relatar o motivo.

## Critérios de Aceite
- `data/curator-results.md` é criado e contém links úteis, descrições e nível de dificuldade para todas as tecnologias listadas em `project-profile.md`.
- Não há mensagens de erro não tratadas; quaisquer falhas são registradas no campo `erros`.
- O agente utiliza Firecrawl quando possível; caso contrário, usa a ferramenta web nativa.
- O formato de saída segue a convenção de listas numeradas sem tabelas.

---

*Este plano de aula complementa o `plano.md` original e o `plano-aula-2.md`, detalhando a implementação do agente Curator.*