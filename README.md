# Lumire AI API Documentation

Build intelligent applications with our comprehensive AI assistant API. Access conversational chat, code generation, and text-to-speech capabilities powered by advanced language models.

## Table of Contents

- [Introduction](#introduction)
- [Getting Started](#getting-started)
- [Authentication](#authentication)
- [Rate Limits](#rate-limits)
- [Chat API](#chat-api)
- [Code Generation API](#code-generation-api)
- [Text-to-Speech API](#text-to-speech-api)
- [Resources](#resources)

## Introduction

The Lumire AI API provides developers with direct access to our intelligent assistant infrastructure. Powered by advanced language models and ElevenLabs, Lumire can engage in natural conversations, generate production-ready code, and provide high-quality voice synthesis.

**Base URL:** `https://lumire-7e2k.onrender.com`

### Features

- Natural language chat with context-aware responses
- Professional code generation (HTML/CSS/JavaScript)
- High-quality text-to-speech with ElevenLabs
- Dual-mode operation (Chat & Code)
- Conversation history support
- Low latency responses

## Getting Started

To get started with the Lumire AI API:

1. Review the documentation for your desired endpoints
2. Test the public endpoints without authentication
3. For production usage, configure your environment
4. Integrate the API into your application

### Quick Example

```bash
curl -X POST https://lumire-7e2k.onrender.com/api/chat \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Hello, who are you?",
    "conversationHistory": [],
    "mode": "chat"
  }'
```

## Authentication

Currently, all endpoints are public and don't require authentication for development purposes. For production deployments, you'll need to implement your own authentication layer.

### CORS Configuration

The API accepts requests from:
- `https://lumire-7e2k.onrender.com`
- `https://www.lumire-7e2k.onrender.com`
- `http://localhost:3000` (for development)

## Rate Limits

API rate limits help ensure fair usage:

| Environment | Requests per minute | Requirements |
|-------------|---------------------|--------------|
| Development | Unlimited | Localhost only |
| Production | As configured | Backend rate limiting |

## Chat API

### POST /api/chat

Engage in natural conversations with Lumire AI assistant.

**Request Body:**

```json
{
  "message": "What is your purpose?",
  "conversationHistory": [
    { "role": "user", "content": "Hello" },
    { "role": "assistant", "content": "Hi! How can I help you?" }
  ],
  "mode": "chat"
}
```

**Response:**

```json
{
  "response": "I'm Lumire, a friendly AI assistant created to help you...",
  "usage": {
    "prompt_tokens": 45,
    "completion_tokens": 120,
    "total_tokens": 165
  }
}
```

[**Full Chat API Documentation →**](./CHAT_API.md)

## Code Generation API

### POST /api/generate-code

Generate production-ready HTML, CSS, and JavaScript code.

**Request Body:**

```json
{
  "prompt": "Create a responsive navbar",
  "conversationHistory": [],
  "language": "html/css/javascript",
  "mode": "code"
}
```

**Response:**

```json
{
  "code": "```html\n<!DOCTYPE html>\n...\n```",
  "usage": {
    "prompt_tokens": 123,
    "completion_tokens": 890,
    "total_tokens": 1013
  }
}
```

[**Full Code Generation API Documentation →**](./CODE_API.md)

## Text-to-Speech API

### POST /api/tts

Convert text to high-quality speech using ElevenLabs.

**Request Body:**

```json
{
  "text": "Hello, I'm Lumire!"
}
```

**Response:**

```
Content-Type: audio/mpeg
<audio stream>
```

[**Full TTS API Documentation →**](./TTS_API.md)

## Error Responses

All endpoints may return error responses in the following format:

```json
{
  "error": "Message is required"
}
```

### HTTP Status Codes

| Status Code | Description |
|-------------|-------------|
| 200 | Success |
| 400 | Bad Request - Invalid parameters |
| 500 | Internal Server Error |

## Best Practices

1. Keep conversation history limited (last 10 exchanges recommended)
2. Handle errors gracefully and provide user feedback
3. Implement proper timeout handling
4. Cache responses when appropriate
5. Use streaming for long responses when available
6. Monitor token usage for cost optimization

## SDKs and Libraries

Coming soon! We're working on official SDKs for:
- JavaScript/TypeScript
- Python
- Go

## Resources

- **Website:** [https://lumire.xyz](https://lumire.xyz)
- **GitHub:** [https://github.com/lumireai/lumire-api](https://github.com/lumireai/lumire-api)
- **Twitter:** [https://x.com/lumireai](https://x.com/lumireai)
- **Health Check:** [https://lumire-7e2k.onrender.com/health](https://lumire-7e2k.onrender.com/health)

## Support

For support, questions, or feature requests:

- Open an issue on [GitHub](https://github.com/lumireai/lumire-api)
- Follow us on [Twitter](https://x.com/lumireai)
- Visit our website at [lumire-7e2k.onrender.com](https://lumire-7e2k.onrender.com)

## Changelog

### v1.0.0 (2024-11-13)
- Initial release
- Chat API endpoint
- Code Generation API endpoint
- Text-to-Speech API endpoint
- CORS configuration for production
