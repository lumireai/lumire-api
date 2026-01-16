# Text-to-Speech API

The TTS API provides high-quality text-to-speech synthesis powered by ElevenLabs, bringing Lumire's voice to life.

## Endpoints

### POST /api/tts

Convert text to natural-sounding speech audio.

#### Request

**Headers:**
```
Content-Type: application/json
```

**Body:**

```json
{
  "text": "Hello! I'm Lumire, your AI assistant."
}
```

**Body Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| text | string | Yes | Text to convert to speech (max 500 characters) |

#### Example Request

```bash
curl -X POST "https://lumire-7e2k.onrender.com/api/tts" \
  -H "Content-Type: application/json" \
  -d '{
    "text": "Welcome to Lumire AI! How can I assist you today?"
  }' \
  --output speech.mp3
```

#### Response

**Content-Type:** `audio/mpeg`

The response is a binary audio stream in MP3 format that can be:
- Played directly in an HTML audio element
- Saved to a file
- Streamed to the client

**Success Response:**
- Status: `200 OK`
- Body: Audio stream (binary data)

---

## Voice Configuration

Lumire uses ElevenLabs' `eleven_multilingual_v2` model with optimized settings:

| Setting | Value | Description |
|---------|-------|-------------|
| Model | `eleven_multilingual_v2` | Multilingual voice model |
| Stability | `0.5` | Voice consistency (0-1) |
| Similarity Boost | `0.75` | Voice similarity to original (0-1) |
| Voice ID | Custom | Lumire's unique voice profile |

**Voice Characteristics:**
- Natural and conversational tone
- Clear articulation
- Warm and friendly personality
- Suitable for multiple languages

---

## Text Limitations

| Limit | Value | Reason |
|-------|-------|--------|
| Max Characters | 500 | API performance and cost optimization |
| Min Characters | 1 | At least one character required |

**For longer text:**
- Split text into chunks of 500 characters or less
- Concatenate audio files on the client side
- Consider summarizing content first

---

## Error Responses

```json
{
  "error": "Text is required"
}
```

### Common Error Codes

| Status Code | Error Message | Description |
|-------------|---------------|-------------|
| 400 | Text is required | Missing or empty text parameter |
| 500 | ElevenLabs API credentials not configured | Server configuration issue |
| 500 | TTS API error | ElevenLabs API error |
| 500 | Failed to generate speech | General server error |

### Example Error Response

```bash
curl -X POST "https://lumire-7e2k.onrender.com/api/tts" \
  -H "Content-Type: application/json" \
  -d '{}'
```

Response:
```json
{
  "error": "Text is required"
}
```

---

## Use Cases

### 1. Browser Playback

```javascript
async function speakText(text) {
  try {
    const response = await fetch('https://lumire-7e2k.onrender.com/api/tts', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ text })
    });

    if (response.ok) {
      const audioBlob = await response.blob();
      const audioUrl = URL.createObjectURL(audioBlob);
      const audio = new Audio(audioUrl);
      await audio.play();
    }
  } catch (error) {
    console.error('TTS error:', error);
  }
}

// Usage
speakText('Hello! Welcome to my application.');
```

### 2. With Volume Control

```javascript
async function speakText(text, volume = 1.0) {
  const response = await fetch('https://lumire-7e2k.onrender.com/api/tts', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ text: text.substring(0, 500) })
  });

  const audioBlob = await response.blob();
  const audioUrl = URL.createObjectURL(audioBlob);
  const audio = new Audio(audioUrl);
  audio.volume = volume; // 0.0 to 1.0
  await audio.play();
}

// Usage
speakText('This is at full volume', 1.0);
speakText('This is at half volume', 0.5);
```

### 3. Download Audio File

```javascript
async function downloadSpeech(text, filename = 'speech.mp3') {
  const response = await fetch('https://lumire-7e2k.onrender.com/api/tts', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ text })
  });

  const blob = await response.blob();
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = filename;
  a.click();
  URL.revokeObjectURL(url);
}

// Usage
downloadSpeech('Hello world!', 'greeting.mp3');
```

### 4. Queue Multiple Speeches

```javascript
class SpeechQueue {
  constructor() {
    this.queue = [];
    this.isPlaying = false;
  }

  async add(text) {
    this.queue.push(text);
    if (!this.isPlaying) {
      await this.play Next();
    }
  }

  async playNext() {
    if (this.queue.length === 0) {
      this.isPlaying = false;
      return;
    }

    this.isPlaying = true;
    const text = this.queue.shift();

    const response = await fetch('https://lumire-7e2k.onrender.com/api/tts', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ text: text.substring(0, 500) })
    });

    const audioBlob = await response.blob();
    const audioUrl = URL.createObjectURL(audioBlob);
    const audio = new Audio(audioUrl);

    audio.onended = () => this.playNext();
    await audio.play();
  }

  clear() {
    this.queue = [];
  }
}

// Usage
const speechQueue = new SpeechQueue();
speechQueue.add('First message');
speechQueue.add('Second message');
speechQueue.add('Third message');
```

---

## Integration with Chat

Combine TTS with Chat API for voice-enabled conversations:

```javascript
async function chatWithVoice(message) {
  // Get text response
  const chatResponse = await fetch('https://lumire-7e2k.onrender.com/api/chat', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      message,
      conversationHistory: [],
      mode: 'chat'
    })
  });

  const chatData = await chatResponse.json();

  // Speak the response
  const ttsResponse = await fetch('https://lumire-7e2k.onrender.com/api/tts', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      text: chatData.response.substring(0, 500)
    })
  });

  const audioBlob = await ttsResponse.blob();
  const audioUrl = URL.createObjectURL(audioBlob);
  const audio = new Audio(audioUrl);
  await audio.play();

  return chatData.response;
}

// Usage
await chatWithVoice('Tell me a fun fact!');
```

---

## Best Practices

1. **Text Preparation**
   - Remove markdown formatting before TTS
   - Strip code blocks and special characters
   - Limit to 500 characters or less
   - Clean up excessive punctuation

2. **Audio Management**
   - Stop previous audio before playing new
   - Clean up blob URLs with `URL.revokeObjectURL()`
   - Implement pause/resume functionality
   - Handle audio errors gracefully

3. **User Experience**
   - Provide visual feedback during speech
   - Allow users to control volume
   - Add mute/unmute toggle
   - Show loading state while generating

4. **Performance**
   - Cache frequently used phrases
   - Implement debouncing for rapid requests
   - Preload common responses
   - Use audio sprite for short phrases

5. **Accessibility**
   - Provide text alternatives
   - Allow users to disable TTS
   - Respect system audio settings
   - Include keyboard controls

---

## Text Cleaning for TTS

Remove unwanted content before sending to TTS:

```javascript
function cleanTextForTTS(text) {
  // Remove code blocks
  let cleaned = text.replace(/```[\s\S]+?```/g, '');

  // Remove inline code
  cleaned = cleaned.replace(/`[^`]+`/g, '');

  // Remove markdown formatting
  cleaned = cleaned.replace(/[*_`]/g, '');

  // Remove multiple newlines
  cleaned = cleaned.replace(/\n+/g, ' ');

  // Remove extra spaces
  cleaned = cleaned.replace(/\s+/g, ' ');

  // Trim and limit length
  cleaned = cleaned.trim().substring(0, 500);

  return cleaned;
}

// Usage
const rawText = "Here's some code: ```javascript\nconst x = 1;\n``` and **bold text**";
const cleanText = cleanTextForTTS(rawText);
// Result: "Here's some code: and bold text"
```

---

## React Integration Example

```jsx
import { useState, useRef } from 'react';

function TTSComponent() {
  const [text, setText] = useState('');
  const [isPlaying, setIsPlaying] = useState(false);
  const [volume, setVolume] = useState(1.0);
  const audioRef = useRef(null);

  const speak = async () => {
    try {
      // Stop current audio if playing
      if (audioRef.current) {
        audioRef.current.pause();
        audioRef.current = null;
      }

      setIsPlaying(true);

      const response = await fetch('https://lumire-7e2k.onrender.com/api/tts', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ text: text.substring(0, 500) })
      });

      if (!response.ok) {
        throw new Error('TTS failed');
      }

      const audioBlob = await response.blob();
      const audioUrl = URL.createObjectURL(audioBlob);
      const audio = new Audio(audioUrl);
      audio.volume = volume;

      audioRef.current = audio;

      audio.onended = () => {
        setIsPlaying(false);
        URL.revokeObjectURL(audioUrl);
      };

      audio.onerror = () => {
        setIsPlaying(false);
        URL.revokeObjectURL(audioUrl);
      };

      await audio.play();
    } catch (error) {
      console.error('TTS error:', error);
      setIsPlaying(false);
    }
  };

  const stop = () => {
    if (audioRef.current) {
      audioRef.current.pause();
      audioRef.current = null;
      setIsPlaying(false);
    }
  };

  return (
    <div>
      <textarea
        value={text}
        onChange={(e) => setText(e.target.value)}
        placeholder="Enter text to speak..."
        maxLength={500}
      />
      <div>
        <label>
          Volume: {Math.round(volume * 100)}%
          <input
            type="range"
            min="0"
            max="1"
            step="0.1"
            value={volume}
            onChange={(e) => setVolume(parseFloat(e.target.value))}
          />
        </label>
      </div>
      <button onClick={speak} disabled={!text || isPlaying}>
        {isPlaying ? 'Speaking...' : 'Speak'}
      </button>
      {isPlaying && (
        <button onClick={stop}>Stop</button>
      )}
    </div>
  );
}
```

---

## Rate Limits

TTS API uses external ElevenLabs API which has its own rate limits.

**Recommendations:**
- Implement client-side debouncing
- Cache common phrases
- Limit concurrent requests
- Show appropriate loading states

---

## Supported Languages

The `eleven_multilingual_v2` model supports:
- English
- Spanish
- French
- German
- Italian
- Portuguese
- Polish
- Dutch
- And many more...

Simply provide text in the desired language!

---

## Resources

- **Website:** [https://lumire.xyz](https://lumire.xyz)
- **GitHub:** [https://github.com/lumireai/lumire-api](https://github.com/lumireai/lumire-api)
- **Twitter:** [https://x.com/lumireai](https://x.com/lumireai)
- **ElevenLabs:** [https://elevenlabs.io](https://elevenlabs.io)
