```markdown
# Faye Chatbot AI Service

A fast, session-based conversational AI for general, nutrition-focused chat—no user profile required. Designed for real-time, interactive Q&A using WebSockets, powered by Claude 3 and OpenAI Grok (fallback).

---

## 🌍 Environments

- **Production** API: `https://ai-foodhak.com`

---

## 🚦 How It Works

1. **Start a Chat Session**  
   - POST to `/faye/chat/start_session` with a `unique_token` (your identifier).
   - Get a WebSocket URL to begin chatting.

2. **Chat over WebSocket**  
   - Connect to the WebSocket endpoint.
   - Send text messages, receive real-time, streamed AI answers.
   - No user health profile is needed—just ask anything!

3. **End the Session**  
   - POST to `/faye/chat/end_session` with your `unique_token` to clean up your chat state.

All endpoints require a Bearer token:  
```

Authorization: Bearer \<API\_KEY>

````

---

## 🛠️ Usage

### 1. Start a Session

#### Production

```bash
curl -X POST https://ai-foodhak.com/faye/chat/start_session \
  -H "Authorization: Bearer <API_KEY>" \
  -H "Content-Type: application/json" \
  -d '{"unique_token": "YOUR_SESSION_TOKEN"}'
````

**Response:**

```json
{
  "session_key": "unique_token:YOUR_SESSION_TOKEN:abcdef-uuid",
  "unique_token": "YOUR_SESSION_TOKEN",
  "status": "Session started successfully",
  "conversation_history": [],
  "websocket_url": "wss://ai-foodhak.com/ws/faye/YOUR_SESSION_TOKEN"
}
```

---

### 2. Real-Time Chat via WebSocket

**WebSocket Endpoint:**

* Production: `wss://ai-foodhak.com/ws/faye/{unique_token}`

**How to use:**

* **Send:** Text messages (plain string, e.g., `"Is tofu healthy?"`)
* **Receive:**

  * `type: streaming` (the AI's response, streamed)
  * `type: message_stop` (AI reply completed)
  * `type: error` (if something goes wrong)

**Python Example:**

```python
import websockets
import asyncio

async def chat():
    uri = "wss://ai-foodhak.com/ws/faye/YOUR_SESSION_TOKEN"
    async with websockets.connect(uri) as ws:
        await ws.send("What are some high-protein vegan foods?")
        async for msg in ws:
            print(msg)

asyncio.run(chat())
```

---

### 3. End a Session

```bash
curl -X POST https://ai-foodhak.com/faye/chat/end_session \
  -H "Authorization: Bearer <API_KEY>" \
  -H "Content-Type: application/json" \
  -d '{"unique_token": "YOUR_SESSION_TOKEN"}'
```

**Success:**

```json
{ "message": "Session ended successfully" }
```

---

### 4. Health Check

```bash
curl https://ai-foodhak.com/health
```

**Success:**

```json
{
  "status": "healthy",
  "redis": "connected"
}
```

---

## 📦 Endpoints

| Method | Endpoint                   | Description              |
| ------ | -------------------------- | ------------------------ |
| POST   | `/faye/chat/start_session` | Start a chat session     |
| WS     | `/ws/faye/{unique_token}`  | Real-time chat WebSocket |
| POST   | `/faye/chat/end_session`   | End a chat session       |
| GET    | `/health`                  | Service health check     |
| GET    | `/`                        | Welcome message          |

---

## ⚡ Features

* **No user profile required:** Chat is general-purpose, open to anyone with a session token.
* **AI-powered:** Claude 3 with Grok fallback for seamless reliability.
* **Session aware:** Conversation state stored in Redis; each session has its own memory.
* **HTML output:** All answers are valid, minimal HTML for easy UI rendering.
* **Secure:** Requires API key for all requests.

---

## 📝 Notes

* Messages are streamed in real-time for low latency.
* Responses are warm, concise, and evidence-based—perfect for nutrition Q\&A, recipes, and more.
* API keys and Redis credentials are required as environment variables.

---