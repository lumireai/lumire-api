# Code Generation API

The Code Generation API allows you to generate production-ready HTML, CSS, and JavaScript code using advanced language models.

## Endpoints

### POST /api/generate-code

Generate clean, functional code for web development projects.

#### Request

**Headers:**
```
Content-Type: application/json
```

**Body:**

```json
{
  "prompt": "Create a responsive navigation bar with a logo and menu items",
  "conversationHistory": [],
  "language": "html/css/javascript",
  "mode": "code"
}
```

**Body Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| prompt | string | Yes | Description of the code to generate or modifications to make |
| conversationHistory | array | No | Previous conversation context for iterative editing |
| language | string | No | Target language (default: "html/css/javascript") |
| mode | string | No | Must be "code" for code generation mode |

#### Example Request

```bash
curl -X POST "https://lumire-7e2k.onrender.com/api/generate-code" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Create a todo list app with add, delete, and mark complete functionality",
    "conversationHistory": [],
    "language": "html/css/javascript",
    "mode": "code"
  }'
```

#### Response

```json
{
  "code": "```html\n<!DOCTYPE html>\n<html>\n<head>\n    <title>Todo List</title>\n</head>\n<body>\n    <h1>My Todo List</h1>\n    <input type=\"text\" id=\"taskInput\" placeholder=\"Add new task...\">\n    <button onclick=\"addTask()\">Add</button>\n    <ul id=\"taskList\"></ul>\n</body>\n</html>\n```\n\n```css\nbody {\n    font-family: Arial, sans-serif;\n    max-width: 600px;\n    margin: 50px auto;\n    padding: 20px;\n}\n```\n\n```javascript\nfunction addTask() {\n    // Task implementation\n}\n```",
  "usage": {
    "prompt_tokens": 250,
    "completion_tokens": 1200,
    "total_tokens": 1450
  }
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| code | string | Generated code with markdown code blocks |
| usage | object | Token usage information from OpenAI |
| usage.prompt_tokens | number | Number of tokens in the prompt |
| usage.completion_tokens | number | Number of tokens in the response |
| usage.total_tokens | number | Total tokens used |

---

## Code Format

The API returns code wrapped in markdown code blocks with language tags:

```
```html
<!DOCTYPE html>
<html>...</html>
```

```css
body { ... }
```

```javascript
function example() { ... }
```
```

**Supported Languages:**
- HTML (```html)
- CSS (```css)
- JavaScript (```javascript or ```js)

---

## Iterative Code Editing

You can modify existing code by including it in the conversation history:

#### Example: Editing Existing Code

```bash
curl -X POST "https://lumire-7e2k.onrender.com/api/generate-code" \
  -H "Content-Type: application/json" \
  -d '{
    "prompt": "Add a dark mode toggle button",
    "conversationHistory": [
      {
        "role": "user",
        "content": "Create a simple landing page"
      },
      {
        "role": "assistant",
        "content": "```html\n<!DOCTYPE html>\n...\n```"
      }
    ],
    "language": "html/css/javascript",
    "mode": "code"
  }'
```

**Best Practices for Editing:**
- Include the previous code generation request/response
- Be specific about what needs to be changed
- Keep conversation history focused on the current project

---

## System Prompt

Lumire's code generation follows these principles:

**Code Quality:**
- Clean, functional code (not fragments or examples)
- Complete HTML structure with DOCTYPE, head, body
- Modern design principles and best practices
- Semantic HTML5 elements
- Well-commented and readable code

**Code Features:**
- Fully working implementations
- No external dependencies unless requested
- Visually appealing with modern styling
- Responsive design by default
- Cross-browser compatible

---

## Model Configuration

| Parameter | Value | Description |
|-----------|-------|-------------|
| Model | `gpt-4o` | Latest GPT-4 Optimized model |
| Temperature | `0.3` | Lower temperature for consistent, predictable code |
| Max Tokens | `4000` | Higher limit for complete code generation |

---

## Error Responses

```json
{
  "error": "Prompt is required"
}
```

### Common Error Codes

| Status Code | Error Message | Description |
|-------------|---------------|-------------|
| 400 | Prompt is required | Missing or empty prompt parameter |
| 500 | Failed to generate code | OpenAI API error or server issue |

---

## Use Cases

### 1. Generate Complete Web Pages

```javascript
const response = await fetch('https://lumire-7e2k.onrender.com/api/generate-code', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    prompt: 'Create a portfolio website with hero section, about, projects, and contact form',
    conversationHistory: [],
    language: 'html/css/javascript',
    mode: 'code'
  })
});

const data = await response.json();
console.log(data.code);
```

### 2. Generate Components

```javascript
const response = await fetch('https://lumire-7e2k.onrender.com/api/generate-code', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    prompt: 'Create a modal dialog component with overlay',
    conversationHistory: [],
    language: 'html/css/javascript',
    mode: 'code'
  })
});
```

### 3. Iterative Development

```javascript
let history = [];

async function generateCode(prompt) {
  const response = await fetch('https://lumire-7e2k.onrender.com/api/generate-code', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      prompt,
      conversationHistory: history.slice(-10),
      language: 'html/css/javascript',
      mode: 'code'
    })
  });

  const data = await response.json();

  // Update history
  history.push({ role: 'user', content: prompt });
  history.push({ role: 'assistant', content: data.code });

  return data.code;
}

// Usage
const code1 = await generateCode('Create a login form');
const code2 = await generateCode('Add password strength indicator');
const code3 = await generateCode('Add remember me checkbox');
```

---

## Code Parsing

The response contains code wrapped in markdown. Here's how to extract it:

### JavaScript Example

```javascript
function parseCode(codeString) {
  const extracted = {
    html: null,
    css: null,
    js: null
  };

  // Extract HTML
  const htmlMatch = codeString.match(/```html\n([\s\S]+?)```/);
  if (htmlMatch) extracted.html = htmlMatch[1].trim();

  // Extract CSS
  const cssMatch = codeString.match(/```css\n([\s\S]+?)```/);
  if (cssMatch) extracted.css = cssMatch[1].trim();

  // Extract JavaScript
  const jsMatch = codeString.match(/```(?:javascript|js)\n([\s\S]+?)```/);
  if (jsMatch) extracted.js = jsMatch[1].trim();

  return extracted;
}

// Usage
const response = await fetch('https://lumire-7e2k.onrender.com/api/generate-code', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    prompt: 'Create a calculator',
    mode: 'code'
  })
});

const data = await response.json();
const code = parseCode(data.code);

console.log(code.html);  // HTML code
console.log(code.css);   // CSS code
console.log(code.js);    // JavaScript code
```

---

## Best Practices

1. **Clear Prompts**
   - Be specific about requirements
   - Mention any constraints or preferences
   - Specify responsive requirements if needed

2. **Iterative Development**
   - Start with a basic structure
   - Add features incrementally
   - Keep conversation history focused

3. **Code Review**
   - Always review generated code
   - Test in multiple browsers
   - Validate HTML and CSS

4. **Security**
   - Sanitize user input in generated code
   - Review for XSS vulnerabilities
   - Don't trust generated code blindly

5. **Performance**
   - Request only necessary features
   - Avoid generating duplicate code
   - Cache results when appropriate

---

## Integration Examples

### React Integration

```jsx
import { useState } from 'react';

function CodeGenerator() {
  const [prompt, setPrompt] = useState('');
  const [code, setCode] = useState({ html: '', css: '', js: '' });
  const [loading, setLoading] = useState(false);

  const generateCode = async () => {
    setLoading(true);
    try {
      const response = await fetch('https://lumire-7e2k.onrender.com/api/generate-code', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          prompt,
          conversationHistory: [],
          mode: 'code'
        })
      });

      const data = await response.json();
      const parsed = parseCode(data.code);
      setCode(parsed);
    } catch (error) {
      console.error('Error:', error);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div>
      <textarea
        value={prompt}
        onChange={(e) => setPrompt(e.target.value)}
        placeholder="Describe what you want to build..."
      />
      <button onClick={generateCode} disabled={loading}>
        {loading ? 'Generating...' : 'Generate Code'}
      </button>
      <div>
        <h3>HTML</h3>
        <pre>{code.html}</pre>
        <h3>CSS</h3>
        <pre>{code.css}</pre>
        <h3>JavaScript</h3>
        <pre>{code.js}</pre>
      </div>
    </div>
  );
}
```

---

## Example Projects

### 1. Landing Page

**Prompt:**
```
Create a modern landing page for a SaaS product with:
- Hero section with headline and CTA button
- Features section with 3 cards
- Pricing section with 3 tiers
- Contact form
- Responsive design
- Dark mode support
```

### 2. Dashboard

**Prompt:**
```
Create a dashboard with:
- Sidebar navigation
- Header with user menu
- Stats cards showing metrics
- Data table with sorting
- Chart placeholder
- Responsive layout
```

### 3. Form Wizard

**Prompt:**
```
Create a multi-step form wizard with:
- 3 steps with progress indicator
- Form validation
- Previous/Next navigation
- Summary on final step
- Smooth transitions
```

---

## Rate Limits

Code Generation API uses more tokens than chat, so be mindful of usage.

**Recommendations:**
- Cache generated code locally
- Implement debouncing for real-time generation
- Show clear loading states
- Allow users to edit and iterate

---

## Resources

- **Website:** [https://lumire.xyz](https://lumire.xyz)
- **GitHub:** [https://github.com/lumireai/lumire-api](https://github.com/lumireai/lumire-api)
- **Twitter:** [https://x.com/lumireai](https://x.com/lumireai)
