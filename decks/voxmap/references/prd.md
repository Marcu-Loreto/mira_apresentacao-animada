# 🛡️ PRD: VozGuard — Sistema Inteligente de Despacho de Emergências

## 1. Visão Geral e Objetivo
O **VozGuard** é uma plataforma de monitoramento e despacho de emergências de última geração. O objetivo é reduzir drasticamente o tempo de resposta em situações críticas, substituindo ou auxiliando triagens humanas por agentes de voz baseados em IA, integrando geolocalização em tempo real e roteamento automatizado de socorristas.

## 2. Personas e Público-Alvo
*   **Operador do Cockpit:** Profissional que gerencia as chamadas, visualiza transcrições e confirma o despacho de rotas.
*   **Responders (Socorristas):** Equipes de campo (ambulâncias, viaturas) que recebem rotas e são monitoradas via GPS.
*   **Gestor de Operações:** Analisa custos, performance dos agentes e histórico de métricas.
*   **Solicitante (Cliente):** Pessoa em situação de emergência que liga para o sistema.

## 3. Requisitos Funcionais

### 3.1. Infraestrutura de Telefonia e IA Conversacional
*   **Atendimento Híbrido:** O sistema deve permitir alternar entre o modo **IA (Luci)** e **Humano** via toggle global.
*   **Agente Luci:** IA de voz (ElevenLabs) capaz de conduzir conversas naturais, coletar dados do incidente e manter latência sub-segundo.
*   **Encaminhamento Inteligente:** Chamadas capturadas podem ser transferidas para humanos via TwiML `<Dial>`, mantendo a gravação e transcrição ativas.

### 3.2. Transcrição e Análise Cognitiva (Intelligence Pipeline)
*   **Streaming de Áudio:** Captura de áudio bidirecional (local ou telefônico) via WebSockets.
*   **Agentes de Análise (LLM):**
    *   **Sentimento:** Detecta urgência e estado emocional do solicitante.
    *   **Classificação:** Categoriza o incidente (ex: Médico, Segurança, Incêndio) e extrai tags.
    *   **Coerência:** Avalia a veracidade do relato para detectar possíveis trotes.
    *   **Supervisor:** Pontua a qualidade do atendimento prestado.
*   **Persistência em Tempo Real:** Cada turno de fala deve ser gravado e exibido instantaneamente no Cockpit via Supabase Realtime.

### 3.3. Geolocalização e Despacho
*   **Tracking do Solicitante:** Envio de SMS automático com link efêmero de geolocalização.
*   **Monitoramento de Responders:** Integração com Traccar Client para rastreio contínuo de posição GPS dos socorristas.
*   **Cálculo de Rotas:** Integração com Google Routes API v2 para calcular trajetos (Carro, Moto, Caminhada) considerando tráfego em tempo real.
*   **Injeção de Instrução:** Operador pode selecionar uma rota e "injetar" a instrução na fala da IA para informar o solicitante sobre a chegada do socorro.

### 3.4. Gestão e Custos
*   **Dashboard Financeiro:** Exibição granular de custos (Twilio, tokens ElevenLabs, minutos de transcrição).
*   **Gestão de Unidades:** Cadastro e monitoramento de status (Online/Offline) de cada base ou veículo de resposta.

## 4. Requisitos Não Funcionais
*   **Latência:** Turno de voz (STT + LLM + TTS) deve ser inferior a 1.2 segundos para garantir naturalidade.
*   **Disponibilidade:** Backend em Edge Functions (Deno) para máxima escalabilidade e baixa latência geográfica.
*   **Segurança:** Implementação de Row Level Security (RLS) no Supabase e expiração automática de links de rastreio (TTL 30min).
*   **Observabilidade:** Logs estruturados por `conversation_id` para auditoria completa de cada incidente.

## 5. Estrutura de Dados Principal (Postgres)
*   `calls`: Metadados da chamada, status e custos.
*   `call_segments`: Transcrições textuais turno a turno.
*   `responders`: Cadastro de unidades de socorro e slots de rastreio.
*   `call_locations / call_routes`: Armazenamento de coordenadas do incidente e caminhos sugeridos.

## 6. Stack Tecnológica
*   **Frontend:** React (Vite) + Tailwind CSS + Lucide React.
*   **Backend:** Supabase (Postgres, Auth, Realtime, Edge Functions).
*   **Telefonia:** Twilio Programmable Voice & Messaging.
*   **IA de Voz:** ElevenLabs (Scribe v2, Conversational AI).
*   **Mapas:** Google Maps Platform (Maps SDK, Geocoding, Routes API).
*   **Tracking:** Protocolo OsmAnd (Traccar).

## 7. Roadmap de Evolução

### Fase 1 (MVP - Concluído)
*   Sincronização de chamadas Twilio ↔ Supabase.
*   Cockpit com mapa e transcrição ao vivo.
*   Envio de SMS de localização e captura de GPS.

### Fase 2 (Atual - WIP)
*   Estabilização da ingestão Traccar para múltiplos dispositivos.
*   Injeção de instruções customizadas no prompt da IA.
*   Melhoria na UX do histórico de custos.

### Fase 3 (Futuro)
*   **App Mobile Responder:** Substituir o Traccar Client por um app nativo para receber ordens de serviço.
*   **Multi-tenant:** Suporte para diferentes organizações (ex: múltiplas prefeituras ou empresas de segurança).
*   **Predição via IA:** Antecipar hotspots de incidentes com base no histórico de chamadas.
