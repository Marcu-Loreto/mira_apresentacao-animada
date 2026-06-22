# 🛠️ Especificações Técnicas (Technical Specs) — VozGuard

Este documento detalha a implementação técnica do sistema VozGuard, servindo como guia para desenvolvedores e referência para manutenção.

## 1. Arquitetura de Dados (Supabase/Postgres)

### 1.1. Principais Tabelas

#### `calls`
Armazena a entidade principal de uma chamada/sessão.
| Coluna | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | UUID | Chave primária interna. |
| `conversation_id` | TEXT | ID único vindo do ElevenLabs ou Twilio (SID). |
| `status` | TEXT | `in_progress`, `completed`, `failed`. |
| `from_number` | TEXT | Número do originador (E.164). |
| `to_number` | TEXT | Número de destino (DID). |
| `started_at` | TIMESTAMPTZ | Início da sessão. |
| `ended_at` | TIMESTAMPTZ | Fim da sessão. |
| `transcript_summary` | TEXT | Resumo gerado por IA após o fim. |

#### `call_segments`
Turnos individuais de fala capturados em tempo real.
| Coluna | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | UUID | Chave primária. |
| `call_id` | UUID | FK para `calls`. |
| `channel` | TEXT | `mic` (cliente) ou `system` (IA/Operador). |
| `text` | TEXT | Conteúdo da fala transcrito. |
| `message_index` | INT | Ordem sequencial na conversa. |
| `ts` | TIMESTAMPTZ | Timestamp do evento. |

#### `responders`
Unidades de socorro cadastradas.
| Coluna | Tipo | Descrição |
| :--- | :--- | :--- |
| `id` | UUID | Chave primária. |
| `name` | TEXT | Nome da unidade (ex: ALPHA-01). |
| `slot` | INT | Identificador para integração Traccar (1 ou 2). |
| `home_lat/lng` | FLOAT8 | Coordenadas da base de origem. |

#### `app_settings`
Configurações globais persistidas (JSONB).
*   `answer_mode`: `{"mode": "ai" | "human", "human_number": "+55..."}`.

## 2. API & Edge Functions (Deno Runtime)

### 2.1. Fluxo de Voz (Twilio)
*   `twilio-voice-twiml`: Recebe o webhook de entrada da Twilio.
    *   **Lógica:** Consulta `app_settings`. Se `ai`, retorna `<Connect><Stream url="..."/></Connect>`. Se `human`, retorna `<Dial>`.
*   `twilio-stream-receiver`: WebSocket que recebe o áudio bruto (μ-law), converte para PCM e envia ao Scribe para monitoramento em tempo real.

### 2.2. Inteligência (Analítica)
*   `analyze-conversation`: Invocada periodicamente ou por webhook.
    *   **Input:** ID da chamada.
    *   **Output:** Grava resultados em colunas JSON nas tabelas de análise (sentiment, categorization, coherence).

### 2.3. Geolocalização
*   `send-location-request`: Gera um `tracking_token` e envia SMS via Twilio Messaging API.
*   `compute-routes`: Recebe origem (responder) e destino (vítima) e consulta Google Routes API v2.
    *   Retorna polylines codificadas e estimativas de tempo (ETA).

## 3. Integrações Externas

| Serviço | Uso | Protocolo |
| :--- | :--- | :--- |
| **ElevenLabs** | Agente Conversacional e STT (Scribe). | WebSocket & HTTPS |
| **Twilio** | Conectividade PSTN e envio de SMS. | TwiML/REST |
| **Google Maps** | Geocoding e Matriz de Rotas. | REST API v2 |
| **Traccar** | Protocolo OsmAnd para GPS de responders. | HTTP POST |

## 4. Variáveis de Ambiente (Secrets)
| Key | Descrição |
| :--- | :--- |
| `ELEVEN_LABS_API_KEY` | Chave de autenticação para voz e transcrição. |
| `TWILIO_ACCOUNT_SID` | ID da conta Twilio. |
| `TWILIO_AUTH_TOKEN` | Token de autenticação Twilio. |
| `GOOGLE_MAPS_API_KEY` | Chave com permissão para Routes, Geocoding e Maps JS. |
| `SUPABASE_SERVICE_ROLE_KEY` | Para bypass de RLS nas Edge Functions. |

## 5. Frontend (React + Vite)
*   **Hook `useIris`:** Gerencia a conexão Realtime com o Supabase para o Cockpit.
*   **Componente `DispatchMap`:** Renderiza o mapa, polylines de rotas e marcadores dinâmicos dos socorristas.
*   **AnswerModeToggle:** Interface em `/settings` para alternar o roteamento de chamadas.

## 6. Fluxo de Segurança
1.  **Operador:** Autenticado via Supabase Auth (Email/Senha).
2.  **Solicitante:** Acesso via Token UUID efêmero na página de `/track`.
3.  **Responders:** Autenticação por `device_id` no endpoint de ingestão.
4.  **Database:** RLS habilitado em todas as tabelas sensíveis.
