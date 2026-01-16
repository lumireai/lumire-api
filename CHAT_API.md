# Chat API

The Chat API allows you to have natural conversations with Lumire AI assistant.

## Endpoints

### POST /api/chat

Send a message to Lumire and receive a conversational response with context awareness.

#### Request

**Headers:**
```
Content-Type: application/json
```

**Body:**

```json
{
  "message": "What is your purpose?",
  "conversationHistory": [
    { "role": "user", "content": "Hello" },
    { "role": "assistant", "content": "Hi! How can I help you today?" }
  ],
  "mode": "chat"
}
```

**Body Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| message | string | Yes | The user's message to send to Lumire |
| conversationHistory | array | No | Previous conversation context (last 10 exchanges recommended) |
| mode | string | No | Must be "chat" for conversational mode |

#### Example Request

```bash
curl -X POST "https://lumire-7e2k.onrender.com/api/chat" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Who created you?",
    "conversationHistory": [],
    "mode": "chat"
  }'
```

#### Response

```json
{
  "response": "I'm Lumire, a friendly and intelligent AI companion created by talented developers who wanted to make AI assistance more personal and friendly. I'm here to help you with various tasks, answer questions, or just have a nice conversation!",
  "usage": {
    "prompt_tokens": 45,
    "completion_tokens": 120,
    "total_tokens": 165
  }
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| response | string | Lumire's response message |
| usage | object | Token usage information from OpenAI |
| usage.prompt_tokens | number | Number of tokens in the prompt |
| usage.completion_tokens | number | Number of tokens in the response |
| usage.total_tokens | number | Total tokens used |

---

## Conversation History Format

The `conversationHistory` parameter should contain previous exchanges in the following format:

```json
[
  { "role": "user", "content": "Hello!" },
  { "role": "assistant", "content": "Hi! How can I help you?" },
  { "role": "user", "content": "What's the weather?" },
  { "role": "assistant", "content": "I don't have access to real-time weather data..." }
]
```

**Best Practices:**
- Include only the last 10 exchanges to avoid token limits
- Always alternate between "user" and "assistant" roles
- Keep the conversation focused and relevant

---

## System Prompt

Lumire uses the following personality traits:

- **Warm, caring, and personable**
- Conversational and natural in responses
- Supportive and encouraging
- Can discuss various topics casually
- Knowledgeable but not robotic
- Slightly playful and engaging

---

## Model Configuration

| Parameter | Value | Description |
|-----------|-------|-------------|
| Model | `gpt-4o` | Latest GPT-4 Optimized model |
| Temperature | `0.8` | Higher creativity for natural conversation |
| Max Tokens | `500` | Response length limit |

---

## Error Responses

All endpoints may return error responses in the following format:

```json
{
  "error": "Message is required"
}
```

### Common Error Codes

| Status Code | Error Message | Description |
|-------------|---------------|-------------|
| 400 | Message is required | Missing or empty message parameter |
| 500 | Failed to process chat request | OpenAI API error or server issue |

### Example Error Response

```bash
curl -X POST "https://lumire-7e2k.onrender.com/api/chat" \
  -H "Content-Type: application/json" \
  -d '{
    "conversationHistory": []
  }'
```

Response:
```json
{
  "error": "Message is required"
}
```

---

## Use Cases

### 1. Simple Chat

```javascript
const response = await fetch('https://lumire-7e2k.onrender.com/api/chat', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    message: 'Hello!',
    conversationHistory: [],
    mode: 'chat'
  })
});

const data = await response.json();
console.log(data.response);
```

### 2. Contextual Conversation

```javascript
let history = [];

async function sendMessage(message) {
  const response = await fetch('https://lumire-7e2k.onrender.com/api/chat', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      message,
      conversationHistory: history.slice(-10), // Keep last 10 exchanges
      mode: 'chat'
    })
  });

  const data = await response.json();

  // Update history
  history.push({ role: 'user', content: message });
  history.push({ role: 'assistant', content: data.response });

  return data.response;
}

// Usage
await sendMessage('Hello!');
await sendMessage('What can you help me with?');
```

### 3. Topic-Specific Chat

```javascript
const response = await fetch('https://lumire-7e2k.onrender.com/api/chat', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    message: 'Explain quantum computing in simple terms',
    conversationHistory: [
      { role: 'user', content: 'I want to learn about technology' },
      { role: 'assistant', content: 'Great! I\'d love to help you learn...' }
    ],
    mode: 'chat'
  })
});
```

---

## Best Practices

1. **Manage Context Length**
   - Keep conversation history to last 10 exchanges
   - Remove old exchanges to stay within token limits

2. **Error Handling**
   - Always check for error responses
   - Implement retry logic for network failures

3. **User Experience**
   - Show loading states while waiting for response
   - Stream responses when possible for better UX

4. **Rate Limiting**
   - Implement client-side debouncing
   - Cache common responses when appropriate

5. **Security**
   - Sanitize user input before sending
   - Don't expose API keys in client-side code

---

## Integration Examples

### React

```jsx
import { useState } from 'react';

function ChatComponent() {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState('');
  const [loading, setLoading] = useState(false);

  const sendMessage = async () => {
    if (!input.trim()) return;

    setLoading(true);
    const userMessage = { role: 'user', content: input };
    const newMessages = [...messages, userMessage];
    setMessages(newMessages);

    try {
      const response = await fetch('https://lumire-7e2k.onrender.com/api/chat', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          message: input,
          conversationHistory: newMessages.slice(-10),
          mode: 'chat'
        })
      });

      const data = await response.json();
      setMessages([...newMessages, {
        role: 'assistant',
        content: data.response
      }]);
    } catch (error) {
      console.error('Error:', error);
    } finally {
      setLoading(false);
      setInput('');
    }
  };

  return (
    <div>
      {messages.map((msg, i) => (
        <div key={i} className={msg.role}>
          {msg.content}
        </div>
      ))}
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
        onKeyPress={(e) => e.key === 'Enter' && sendMessage()}
      />
      <button onClick={sendMessage} disabled={loading}>
        {loading ? 'Sending...' : 'Send'}
      </button>
    </div>
  );
}
```

---

## Rate Limits

Chat API endpoints are subject to the configured rate limits on your backend instance.

**Recommendations:**
- Implement debouncing for rapid requests
- Show appropriate loading states
- Handle rate limit errors gracefully

---

## Resources

- **Website:** [https://lumire-7e2k.onrender.com](https://lumire-7e2k.onrender.com)
- **GitHub:** [https://github.com/lumireai/lumire-api](https://github.com/lumireai/lumire-api)
- **Twitter:** [https://x.com/lumireai](https://x.com/lumireai)
