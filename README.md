# 🤖 DSL Chatbot Platform

A production-ready chatbot platform to define, compile, and deploy chatbots using a custom Domain-Specific Language.

🌐 **Live Demo:** [buildchatbot.onrender.com](https://buildchatbot.onrender.com)

[![Node.js](https://img.shields.io/badge/Node.js-43853D?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![MongoDB](https://img.shields.io/badge/MongoDB-4EA94B?style=for-the-badge&logo=mongodb&logoColor=white)](https://mongodb.com)
[![Express](https://img.shields.io/badge/Express.js-000000?style=for-the-badge&logo=express&logoColor=white)](https://expressjs.com)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)](https://javascript.com)

---

## ✨ Features

| Feature | Description |
|--------|-------------|
| **Custom DSL Compiler** | Define chatbots with a simple, intuitive DSL |
| **MongoDB Storage** | Persistent storage with connection pooling |
| **Runtime Engine** | Optimized message matching with multiple strategies |
| **Embeddable Widget** | Beautiful, responsive chat widget for any website |
| **Web Dashboard** | Compile and manage bots through a web interface |
| **REST API** | Full CRUD operations for bot management |
| **Production Ready** | Error handling, validation, rate limiting, and logging |

---

## 🏗️ Architecture

```
DSL → Compiler → JSON → MongoDB
                    ↓
Widget ← API ← Runtime Engine
```

**Compiler Pipeline:** Lexer → Parser → Semantic Analyzer → IR Generator  
**Runtime Engine:** Message matching with keyword indexing and trie-based search  
**API Layer:** RESTful endpoints with validation and error handling  
**Frontend:** Vanilla JS widget + HTML dashboard

---

## 🚀 Quick Start

```bash
# 1. Clone the repository
git clone https://github.com/Aartipaal/dsl-chatbot-platform.git
cd dsl-chatbot-platform

# 2. Install dependencies
npm install

# 3. Configure environment
cp .env.example .env

# 4. Start the server
npm start
```

Open the dashboard at `http://localhost:5000`

---

## ⚙️ Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `MONGODB_URI` | ✅ | MongoDB connection string |
| `DB_NAME` | ✅ | Database name (e.g. `dsl_chatbot`) |
| `PORT` | — | Server port (default: `5000`) |
| `NODE_ENV` | — | `development` or `production` |
| `JWT_SECRET` | — | Secure random string |
| `REDIS_URL` | — | Redis connection string (optional) |

```env
MONGODB_URI=mongodb://localhost:27017/dsl_chatbot
DB_NAME=dsl_chatbot
PORT=5000
NODE_ENV=production
```

---

## 📝 DSL Syntax

```
bot MyChatbot
domain ecommerce
tone friendly

welcome "Hello! How can I help you today?"

intent product_search
keywords: shirts pants dresses shoes
response "Great choice! What size are you looking for?"

intent order_status
keywords: order status tracking
response "Please provide your order number."

fallback "I'm sorry, I didn't understand. Can you rephrase?"
```

### DSL Reference

| Keyword | Description |
|---------|-------------|
| `bot <name>` | Bot name |
| `domain <type>` | `ecommerce`, `education`, `saas` |
| `tone <style>` | `friendly`, `formal`, `professional` |
| `welcome "<message>"` | Welcome message |
| `intent <name>` | Define an intent |
| `keywords: word1 word2` | Keywords for matching |
| `response "<message>"` | Bot response |
| `fallback "<message>"` | Default response |

---

## 📡 API Reference

### Compile DSL
```http
POST /api/compile
Content-Type: application/json

{
  "dsl": "bot MyBot\ndomain ecommerce..."
}
```

### Chat with Bot
```http
POST /api/chat/{botId}
Content-Type: application/json

{
  "message": "Hello"
}
```

### Other Endpoints

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/bot/{botId}` | Get bot info |
| GET | `/api/bots` | List all bots |
| DELETE | `/api/bot/{botId}` | Delete bot |
| GET | `/api/health` | Health check |

---

## 🎨 Embedding the Widget

Add this single line to any website:

```html
<script src="https://buildchatbot.onrender.com/widget.js" data-bot-id="bot_abc123"></script>
```

---

## 🧪 Testing

### Using cURL

```bash
# Compile a bot
curl -X POST http://localhost:5000/api/compile \
  -H "Content-Type: application/json" \
  -d '{
    "dsl": "bot TestBot\ndomain ecommerce\ntone friendly\nwelcome \"Hello!\"\nintent greet\nkeywords: hello hi\nresponse \"Hi there!\"\nfallback \"Sorry\""
  }'

# Chat with the bot
curl -X POST http://localhost:5000/api/chat/bot_abc123 \
  -H "Content-Type: application/json" \
  -d '{"message": "hello"}'
```

### Using the Web Interface
1. Visit `http://localhost:5000`
2. Paste DSL code in the left panel
3. Click **"Compile Bot"**
4. Copy the embed code and use the bot ID

---

## 📁 Project Structure

```
├── src/
│   ├── compiler/
│   │   ├── index.js       # Compiler facade
│   │   ├── lexer.js       # Lexical analysis
│   │   ├── parser.js      # Syntax analysis
│   │   ├── semantic.js    # Semantic validation
│   │   └── ir.js          # IR generation
│   ├── engine/
│   │   ├── runtime.js     # Runtime manager
│   │   └── matcher.js     # Message matching
│   ├── storage/
│   │   ├── mongodb.repository.js
│   │   ├── cache.js
│   │   └── initDb.js
│   ├── api/
│   │   ├── routes.js
│   │   ├── controllers/
│   │   └── middleware/
│   ├── errors/
│   └── server.js
├── public/
│   ├── index.html         # DSL Editor UI
│   └── widget.js          # Chat widget
├── tests/
├── templates/
├── .env.example
├── package.json
└── README.md
```

---

## 🐳 Production Deployment

```dockerfile
FROM node:16-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 5000
CMD ["npm", "start"]
```

### Deploying to Render

1. Remove committed secrets:
```bash
git rm --cached .env || true
git commit -m "Remove local .env from repo"
```

2. Create a new **Web Service** on [render.com](https://render.com) and connect your repo

3. Set environment variables in Render dashboard:
   - `MONGODB_URI`, `DB_NAME`, `NODE_ENV=production`, `JWT_SECRET`

4. Build command: `npm install` · Start command: `npm start`

5. Verify deployment at `https://<your-service>.onrender.com/api/health`

---

## 🏃 Development

```bash
npm run dev      # Auto-restart on changes
npm test         # Run tests
npm run lint     # Lint code
npm run format   # Format code
```

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m "Add my feature"`
4. Push and submit a pull request

---

## 🌐 Connect

**Aarti** — [Portfolio](https://aartiii.netlify.app) · [GitHub](https://github.com/Aartipaal) · [Gmail](mailto:aarti190920@gmail.com) · [Twitter](https://x.com/AartiP42890)
