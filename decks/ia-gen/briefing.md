# Briefing: IA Generativa

**Fonte:** ia-generativa-conceitos-praticos.html + letramento-ia-generativa-clevels.html — decks/ia-gen/references/
**Data da extração:** 2026-06-22

## Essência em uma frase

Capacitação completa em IA Generativa, do Transformer aos Agentes: conceitos, arquitetura, implementação prática e maturidade organizacional, para profissionais técnicos e líderes que precisam tomar decisões informadas sobre soluções de IA.

## Conceitos-chave (candidatos a slide)

1. Evolução 2017-2025+ — linha do tempo: Transformer, GPT, ChatGPT, Tools, Multimodal, Agentes. Cada geração adicionou uma capacidade. (sugestão visual: timeline)
2. LLM: o que é e o que NÃO é — motor de linguagem, não base de conhecimento. Precisa de fontes, controles e governança. (sugestão visual: comparacao)
3. Tokens e economia — unidade de custo. Janela de contexto como "mesa de trabalho". Roteamento de modelos por complexidade. (sugestão visual: metricas)
4. Prompt Engineering — zero-shot, few-shot, chain-of-thought, role prompting, structured output. Prompt como ativo de produto. (sugestão visual: d3-fluxo)
5. RAG: Retrieval-Augmented Generation — padrão dominante (70% das apps enterprise). Pipeline offline (ingestão) + online (consulta). Chunking, embeddings, busca vetorial, reranker. (sugestão visual: d3-fluxo)
6. Guardrails — defesa em camadas: input, output, topical, factual. Frameworks: NeMo, GuardrailsAI, Lakera. (sugestão visual: escada)
7. Tools / Function Calling — quando a IA deixa de conversar e passa a agir. Riscos e controles. (sugestão visual: orbital)
8. Memória — short-term (conversa), long-term (usuário), corporativa (RAG). Implementação com Redis, Postgres, Vector Store. (sugestão visual: comparacao)
9. Harness: o produto completo — LLM é 20% do esforço, harness é os outros 80%. Interface + Orquestrador + RAG + Tools + Guardrails + Observabilidade + Avaliação. (sugestão visual: orbital)
10. Observabilidade — traces, spans, métricas, custo. Ferramentas: Langfuse, LangSmith, Arize Phoenix, OpenTelemetry. (sugestão visual: metricas)
11. Testes em IA Generativa — retrieval, geração, agente, produção. AI as a Judge. Avaliar distribuições, não respostas exatas. (sugestão visual: escada)
12. Agentes e padrão ReAct — Observe, Think, Act em loop. Chatbot vs Assistente vs Agente vs Sistema Agentic. (sugestão visual: d3-fluxo)
13. Orquestração com LangGraph — grafos, estado compartilhado, checkpoints, human-in-the-loop, subgrafos. (sugestão visual: orbital)
14. Multi-agente — padrões supervisor, hierárquico, colaborativo. Trade-offs: mais agentes = mais custo e complexidade. (sugestão visual: comparacao)
15. Maturidade organizacional — 5 estágios: experimentação, pilotos, integrado, plataforma, agentic. (sugestão visual: escada)
16. Gestão de custo — roteamento de modelos, cache semântico, prompt caching, batch processing. (sugestão visual: metricas)

## Dados e números

- Mercado global de IA Generativa: US$71 bi em 2025, projeção US$890 bi em 2032 (CAGR 43,4%) — MarketsandMarkets
- RAG presente em 70% das aplicações enterprise de GenAI — IDC 2025
- Chunking responde por 30-40% da qualidade de retrieval — Vectara/NAACL 2025
- Chunk semântico é 40-60% mais preciso que chunking fixo — DigitalApplied
- 73% das empresas exigem monitoramento de agentes, mas 63% citam falta de ferramental — MLflow 2026
- GPT-4o: US$2,50/1M input + US$10/1M output
- Latência alvo: turno de voz STT+LLM+TTS < 1,2 s
- Framework de 12 métricas para avaliação de agentes — Towards Data Science 2025

## Narrativa sugerida

Contexto (por que GenAI importa) → Fundamentos (Transformer, LLM, Tokens) → Instrução (Prompts) → Conhecimento (RAG completo) → Segurança (Guardrails) → Ação (Tools + Agentes) → Produto (Harness + Observabilidade + Testes) → Orquestração (LangGraph + Multi-agente) → Maturidade e Custo → Síntese + CTA

## Lacunas

- Não há métricas específicas da organização/empresa destino (dados de uso próprio, benchmarks internos).
- Não há casos de uso concretos da empresa: o material é genérico/educativo.
- A segunda referência (C-Levels) foca no vocabulário executivo; a primeira é técnica profunda. O deck pode ter dois públicos com necessidades diferentes.
