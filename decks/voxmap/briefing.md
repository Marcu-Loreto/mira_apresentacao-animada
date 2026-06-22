# Briefing: VoxMap

**Fonte:** Live Stream Transcriber (projeto de código + docs) — decks/voxmap/references/ (prd.md, specs.md, README.md)
**Data da extração:** 2026-06-16
**Nome oficial:** VoxMap (ignorar os nomes "VozGuard" e "Live Transcribe" das fontes)

## Essência em uma frase

VoxMap é uma plataforma de despacho de emergências que une IA de voz, transcrição cognitiva em tempo real e geolocalização para reduzir drasticamente o tempo de resposta na Segurança Pública. Inteligência, Localização e Ação num único cockpit.

## Conceitos-chave (candidatos a slide)

1. O problema do tempo de resposta — cada segundo conta numa emergência; triagem humana é gargalo. (sugestão visual: metricas)
2. Agente de voz Luci (ElevenLabs) — IA conversacional que atende, coleta dados do incidente e mantém latência sub-segundo, com toggle IA/Humano. (sugestão visual: d3-fluxo)
3. Pipeline de inteligência cognitiva — quatro agentes LLM rodando sobre a transcrição: Sentimento, Classificação, Coerência (anti-trote) e Supervisor de qualidade. (sugestão visual: comparacao)
4. Transcrição em tempo real — áudio bidirecional via WebSocket, persistido turno a turno e exibido instantaneamente no Cockpit (Supabase Realtime). (sugestão visual: d3-fluxo)
5. Geolocalização e despacho — SMS com link efêmero para localizar o solicitante, rastreio GPS dos socorristas (Traccar) e rotas em tempo real (Google Routes API v2). (sugestão visual: d3-fluxo)
6. Injeção de instrução na fala da IA — operador seleciona a rota e a IA informa o solicitante sobre a chegada do socorro. (sugestão visual: timeline)
7. Arquitetura serverless — Edge Functions (Deno) no Supabase para escala e baixa latência geográfica. (sugestão visual: codigo)
8. Segurança e auditoria — RLS no Supabase, tokens de rastreio efêmeros (TTL 30min), logs estruturados por conversation_id. (sugestão visual: comparacao)
9. Roadmap — MVP concluído, estabilização atual (Traccar multi-dispositivo, injeção de instrução) e futuro (app mobile responder, multi-tenant, predição de hotspots). (sugestão visual: timeline)

## Dados e números

- Latência do turno de voz (STT + LLM + TTS): meta < 1,2 s.
- Link de rastreio efêmero: TTL de 30 minutos.
- 4 agentes de análise (sentimento, classificação, coerência, supervisor).
- 3 modais de rota: carro, moto, caminhada (com tráfego em tempo real).
- Métricas de exemplo no template: 42 ms, 99%, 10k (placeholders a substituir por números reais do projeto).

## Trechos de código emblemáticos

- Roteamento TwiML: se modo `ai`, retorna `<Connect><Stream/>`; se `human`, retorna `<Dial>` (Edge Function `twilio-voice-twiml`).
- `compute-routes`: recebe origem (responder) e destino (vítima), consulta Google Routes API v2 e retorna polylines + ETA.

## Narrativa sugerida

Problema (tempo de resposta e gargalo da triagem) → Solução (VoxMap: voz + inteligência + mapa) → Como funciona (atendimento Luci → pipeline de 4 agentes → cockpit em tempo real → geolocalização e rotas) → Diferenciais (latência sub-segundo, anti-trote, arquitetura edge, segurança) → Resultados/Roadmap → CTA.

## Nome do produto

O nome oficial para toda a apresentação é **VoxMap**. As fontes citam "VozGuard" e "Live Transcribe", mas esses nomes NÃO devem aparecer nos slides: usar sempre VoxMap.

## Lacunas

- Métricas reais de impacto (redução de tempo de resposta, volume de chamadas, precisão da classificação) não constam nas fontes; os números do template são placeholders.
- Não há identidade visual/logo nas fontes; o deck usa o tema mira-dark com destaque azul.
