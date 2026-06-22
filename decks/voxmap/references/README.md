# Live Transcribe + Agentes de Conversação

Aplicação web para **transcrever conversas em tempo real** e analisá-las com 4 agentes de IA (sentimento, classificação, roteamento, supervisor).

Suporta dois modos de captura:

1. **Local (microfone + áudio do sistema)** — usa Web Speech / MediaRecorder no navegador. Útil para reuniões Teams/Zoom abertas na máquina.
2. **Telefonia ElevenLabs (via Twilio)** — captura conversas reais de uma linha telefônica atendida por um agente ElevenLabs, com polling quase em tempo real.

## Arquitetura

```
┌──────────────────────────────────────────────────────────────────────┐
│  MODO 1 — Captura local (navegador)                                  │
│  Microfone ─┐                                                        │
│             ├─► useDualTranscription (Scribe) ─► UI ─► Agentes (LLM) │
│  Tela/Aba ──┘                                                        │
└──────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────┐
│  MODO 2 — Telefonia (Twilio + ElevenLabs)                            │
│                                                                      │
│  Cliente ──► Twilio (+55 11 4040-2737) ──► Agente ElevenLabs         │
│                                                  │                   │
│                                                  ▼                   │
│            Edge Function `poll-conversations`                        │
│            ├─ GET /v1/convai/conversations?agent_id=…                │
│            └─ GET /v1/convai/conversations/{id}                      │
│                                                  │                   │
│                                                  ▼                   │
│            Tabelas `calls` / `call_segments` (Postgres + Realtime)   │
│                                                  │                   │
│                                                  ▼                   │
│            UI assina via Supabase Realtime ─► Agentes (LLM)          │
└──────────────────────────────────────────────────────────────────────┘
```

## Stack

- **Frontend**: React 18, Vite 5, TypeScript 5, Tailwind 3, shadcn-ui
- **Backend**: Lovable Cloud (Supabase) — Postgres, Realtime, Edge Functions (Deno)
- **STT (modo local)**: ElevenLabs Scribe via WebSocket
- **STT (modo telefônico)**: ElevenLabs Conversational AI (built-in)
- **LLM dos agentes**: Lovable AI Gateway (Gemini / GPT-5)
- **Telefonia**: Twilio (número +55 11 4040-2737) → SIP/Voice → ElevenLabs

## Tabelas

| Tabela | Descrição |
|---|---|
| `calls` | Uma linha por chamada. Guarda `conversation_id` (ElevenLabs), `agent_id`, `from_number`, `to_number`, `status`, `started_at`, `ended_at`. |
| `call_segments` | Cada turno de fala. Liga a `calls`, com `channel` (`mic` = cliente / `system` = agente), `text`, `message_index` (ordem) e `ts`. |

Ambas com **Realtime** habilitado e **RLS** (leitura pública no MVP, escrita só via service role).

## Edge Functions

- **`poll-conversations`** — chamada periodicamente pelo frontend; busca a conversa mais recente do `ELEVENLABS_AGENT_ID` e sincroniza segmentos novos no banco.
- **`analyze-conversation`** — roda os 4 agentes sobre o transcript acumulado.
- **`scribe-token`** — emite token efêmero para o WebSocket de STT (modo local).

## Setup local

```bash
bun install
bun run dev
```

O `.env` é gerenciado automaticamente pela Lovable Cloud (não editar).

## Requisitos externos

Veja **[`requirements.txt`](./requirements.txt)** — lista de contas, chaves e configurações de terceiros necessárias antes de rodar em produção.

## Roadmap curto

- [x] Captura local (mic + sistema) com Scribe
- [x] 4 agentes analisando o transcript
- [x] Tabelas `calls` / `call_segments` com Realtime
- [x] Edge Function de polling ElevenLabs
- [ ] Toggle de modo na UI (local ↔ telefonia)
- [ ] Webhook `post_call_transcription` como backup pós-chamada
- [ ] Autenticação + multi-usuário
