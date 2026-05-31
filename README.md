# StackAdvisor

## 1. Nome e descrição do projeto
**StackAdvisor** – um orquestrador multi‑agente que ajuda desenvolvedores a escolher a stack tecnológica ideal para seu projeto, validar as decisões com comparações reais, encontrar tutoriais/documentação e treinar o time por meio de simulações técnicas.

## 2. Demonstração do fluxo
1. **Quiz** – o usuário responde a perguntas sobre tipo de projeto, equipe, prazo, orçamento, etc.  
2. **Stack recomendada** – a partir das respostas o StackAdvisor gera `data/project-profile.md` com a stack sugerida (frontend, backend, banco, deploy, observabilidade).  
3. **Comparações reais** – ao escolher a opção **A**, o agente **Scout** usa Firecrawl para buscar comparações reais entre as tecnologias (prós, contras, casos de uso).  
4. **Tutoriais e documentação** – ao escolher a opção **B**, o agente **Curator** coleta tutoriais, docs oficiais e cursos, indicando nível de dificuldade.  
5. **Simulação técnica** – ao escolher a opção **C**, o agente **Coach** faz 5 perguntas técnicas, dá feedback imediato e, ao final, entrega uma pontuação e áreas de melhoria.

## 3. Arquitetura com os 4 agentes
```
┌─────────────────────────────────────┐
│            StackAdvisor              │
│  (orquestrador, quiz, menu)         │
└───────┬───────────────┬──────────────┘
        │               │
        ▼               ▼
   ┌─────────┐    ┌───────────┐
   │  Scout  │    │  Curator  │
   │ (compar│    │ (tutoriais│
   │  ões)   │    │  e docs)  │
   └─────┬───┘    └─────┬─────┘
         │            │
         ▼            ▼
      ┌───────────────┐
      │   Coach       │
      │ (simulação   │
      │  técnica)    │
      └───────────────┘
```

## 4. Estrutura de diretórios completa
```
stack-advisor/
├─ AGENTS.md                     # instruções de inicialização do orquestrador
├─ plano.md                      # plano geral do projeto
├─ plano-aula-2.md               # plano para o agente Scout
├─ plano-aula-3.md               # plano para o agente Curator
├─ plano-aula-4.md               # plano para o agente Coach
├─ data/
│   ├─ project-quiz.md           # respostas do quiz (Concluído: true/false)
│   ├─ project-profile.md        # stack recomendada e justificativa
│   ├─ scout-results.md          # comparações reais (gerado pelo Scout)
│   ├─ curator-results.md        # tutoriais/documentação (gerado pelo Curator)
│   └─ coach-results.md          # histórico da sessão de perguntas (gerado pelo Coach)
├─ personas/
│   ├─ stackadvisor.md           # persona do orquestrador
│   ├─ scout.md                  # persona do Scout
│   ├─ curator.md                # persona do Curator
│   └─ coach.md                  # persona do Coach
├─ skills/
│   ├─ dispatch.md               # protocolo de despacho/handoff
│   ├─ scout.md                  # skill do Scout
│   ├─ curator.md                # skill do Curator
│   └─ coach.md                  # skill do Coach
└─ README.md                     # (este documento)
```

## 5. Como usar (passo a passo no Zed)
1. **Abrir o projeto** no Zed.  
2. **Iniciar o agente** – abra `AGENTS.md`; o StackAdvisor lê automaticamente `personas/stackadvisor.md` e executa o **Fluxo de Inicialização**.  
3. **Responder o quiz** – o agente conduzirá as perguntas (tipo de projeto, equipe, prazo, orçamento, etc.).  
4. **Ver a stack recomendada** – ao concluir o quiz, o StackAdvisor exibirá o resumo da stack (`data/project-profile.md`).  
5. **Escolher a ação** – o menu oferece:  
   - **A** – comparar tecnologias (despacha Scout)  
   - **B** – buscar tutoriais (despacha Curator)  
   - **C** – simular perguntas técnicas (despacha Coach)  
   - **D** – refazer o quiz  
6. **Interagir** – siga as perguntas do sub‑agente escolhido; os resultados são gravados nos arquivos `data/*-results.md`.  
7. **Revisar** – abra os arquivos de resultados para ver comparações, links úteis ou o histórico da sessão técnica.  

> **Obs.:** Firecrawl já está instalado e disponível como CLI (`firecrawl`). Caso o CLI falhe, os agentes utilizam a busca web nativa do Zed como fallback.

## 6. Tecnologias utilizadas
| Tecnologia | Uso no projeto |
|------------|----------------|
| **Zed Agent** | Motor de orquestração (persona, skill, `spawn_agent`, `terminal`, etc.). |
| **Firecrawl CLI** | Busca e scraping de conteúdo web (comparações reais, tutoriais, cursos). |
| **Markdown** | Formato de armazenamento de estado (`data/*.md`) e de comunicação entre agentes. |
| **Git** (opcional) | Controle de versão do código‑fonte. |

## 7. O que cada agente faz
| Agente | Responsabilidade curta |
|--------|------------------------|
| **StackAdvisor** | Conduz o quiz, gera o perfil do projeto, apresenta o menu e despacha os sub‑agentes. |
| **Scout** | Busca comparações reais entre as tecnologias recomendadas (prós, contras, casos de uso). |
| **Curator** | Coleta tutoriais, documentação oficial e cursos, indicando nível de dificuldade (iniciante, intermediário, avançado). |
| **Coach** | Simula 5 perguntas técnicas, fornece feedback imediato e entrega pontuação + áreas de melhoria. |

## 8. Status do projeto
- **Fase 1 – Orquestrador** – concluída (quiz, geração de stack, menu).  
- **Fase 2 – Scout** – concluída (comparações reais, fallback funcional).  
- **Fase 3 – Curator** – concluída (coleta de recursos, fallback funcional).  
- **Fase 4 – Coach** – concluída (simulação de perguntas, feedback, pontuação, histórico).  

> Todas as fases descritas nos planos (`plano.md`, `plano-aula-2.md`, `plano-aula-3.md`, `plano-aula-4.md`) foram implementadas e testadas.

## 9. Autor
**Guilherme Silva** – desenvolvedor e arquiteto do StackAdvisor  
[LinkedIn](https://www.linkedin.com/in/guilherme-silva-b128bb1bb)