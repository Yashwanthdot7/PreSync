<div align="center">

# 🎞️ PreSync

**Real-time collaborative presentations and documents, with live presenting and an AI Copilot.**

![Node.js](https://img.shields.io/badge/Node.js-18%2B-339933?logo=nodedotjs&logoColor=white)
![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=black)
![Vite](https://img.shields.io/badge/Vite-646CFF?logo=vite&logoColor=white)
![Express](https://img.shields.io/badge/Express-000000?logo=express&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?logo=mongodb&logoColor=white)
![Socket.IO](https://img.shields.io/badge/Socket.IO-010101?logo=socketdotio&logoColor=white)
![Tests](https://github.com/Anush-HM/PreSync/actions/workflows/backend-tests.yml/badge.svg)

</div>

---

## 📖 About

PreSync lets teams build slide decks and documents together in real time, import existing PowerPoint, PDF and Word files, and present live to an audience that joins with a code. An AI Copilot suggests edits that apply only when you approve them.

## ✨ Features

- 🎨 **Slide editor:** text, shapes, tables, charts, pictures, layouts, themes, templates, smart guides and full undo/redo.
- 👥 **Real-time collaboration:** co-editing with live cursors, comments, version history and sharing.
- 🔄 **Import and export:** import `.pptx`, `.pdf` and `.docx` as editable content; export to PDF, PPTX, DOCX, PNG and JPEG.
- 📝 **Word documents:** a paginated editor with headers, footers, page numbers, tables and pictures.
- 📡 **Live presenting:** presenter view with notes and next slide; the audience joins by code, link or QR code, with live Q&A.
- 🤖 **AI Copilot:** rewrites, summaries, speaker notes, deck generation and design checks, applied only on approval.

📚 See [docs/FEATURES.md](docs/FEATURES.md) for the full feature reference.

## 🛠️ Tech stack

| Layer | Technologies |
| --- | --- |
| Frontend | React 18, Vite, React Router, Fabric.js, TipTap, Yjs, Socket.IO client |
| Backend | Node.js, Express, MongoDB (Mongoose), Socket.IO, Yjs over WebSocket, JWT auth |
| Files | PDF.js, PDFKit, PptxGenJS, JSZip |
| AI | Anthropic SDK |
| Testing | Vitest, Testing Library, ESLint, GitHub Actions |

## 🚀 Getting started

### 📋 Prerequisites

- Node.js 18 or newer
- MongoDB, local or hosted. For a quick local instance: `docker run -d -p 27017:27017 --name presync-mongo mongo:7`
- An Anthropic API key (optional, only for AI features)

### 📦 Installation

```bash
git clone https://github.com/Anush-HM/PreSync.git
cd PreSync
npm install
npm run install:all
cp backend/.env.example backend/.env
```

### 🔐 Environment variables

Set these in `backend/.env`:

| Variable | Required | Description |
| --- | --- | --- |
| `MONGO_URI` | Yes | MongoDB connection string, e.g. `mongodb://localhost:27017/presync` |
| `JWT_SECRET` | Yes | Long random string used to sign login tokens |
| `CLIENT_ORIGIN` | Yes | Frontend URL allowed by CORS, e.g. `http://localhost:5173` |
| `PORT` | No | Backend port (default `4000`) |
| `ANTHROPIC_API_KEY` | No | Turns on AI Copilot and AI-drafted Q&A answers |

Optional AI settings (`AI_MODEL`, `AI_EFFORT`, `AI_RATE_LIMIT_PER_MINUTE` and more) are documented in [`backend/.env.example`](backend/.env.example). To point the frontend at a different backend, set `VITE_API_URL` in `frontend/.env`.

### ▶️ Run

```bash
npm run dev
```

The frontend opens at **http://localhost:5173** and the API runs at **http://localhost:4000**.

## 📜 Scripts

| Command | Description |
| --- | --- |
| `npm run dev` | Start the backend and frontend together |
| `npm run install:all` | Install backend and frontend dependencies |
| `npm test --prefix backend` | Run backend tests (no database needed) |
| `npm test --prefix frontend` | Run frontend tests |
| `npm run lint --prefix frontend` | Lint the frontend |
| `npm run build --prefix frontend` | Build the frontend for production |

## 📁 Project structure

```
PreSync/
├── backend/
│   └── src/
│       ├── server.js      # HTTP, Socket.IO and Yjs servers
│       ├── app.js         # Express app and routes
│       ├── routes/        # API routes
│       ├── controllers/   # Request handlers
│       ├── models/        # MongoDB schemas
│       ├── services/      # Import, export and Q&A logic
│       ├── sockets/       # Live presenting events
│       ├── yjs/           # Real-time co-editing
│       └── ai/            # AI Copilot
├── frontend/
│   └── src/
│       ├── pages/         # App screens
│       ├── components/    # UI components
│       ├── data/          # API clients and editor logic
│       └── styles/        # Theme
└── docs/                  # Feature reference
```

## 🤝 Contributing

1. Fork the repo and create a branch from `main`.
2. Make your changes and add tests where they apply.
3. Run the tests, lint and build before you commit.
4. Open a pull request that describes your change.
