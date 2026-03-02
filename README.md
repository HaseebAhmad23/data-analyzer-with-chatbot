# Data Analyzer AI Chatbot

A personal project to build website integrated with chatbot for analyzing data using OpenAI and LangChain. Either you can upload a PDF file or drop in a website URL, and then you can chat with an AI chatbot about whatever is in that document or webpage. Built with FastAPI on the backend and Vue 3 on the frontend.

![Vue 3](https://img.shields.io/badge/Vue-3-42b883?style=for-the-badge&logo=vue.js&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-bf5427?style=for-the-badge&logo=fastapi&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.10+-3776ab?style=for-the-badge&logo=python&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-green?style=for-the-badge&logo=css3&logoColor=white)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4o--mini-bf5427?style=for-the-badge&logo=openai&logoColor=white)


## What it does

- Upload a PDF or enter any website URL
- The backend processes the content, splits it into chunks, and creates a FAISS vector store from it
- You open a chat and ask anything — the AI retrieves the most relevant chunks and answers based on your data

## Tech Stack

- **Frontend** — Vue 3, Vite, Axios
- **Backend** — FastAPI, LangChain, OpenAI (GPT-4o-mini + embeddings), FAISS

## Getting Started

### Requirements

- Python 3.10+
- Node 18+
- An OpenAI API key

### Backend

```bash
cd backend
python -m venv venv
source venv/bin/activate    # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

Copy the env file and add your key:

```bash
cp .env.example .env
# open .env and set OPENAI_API_KEY
```

Start the server from the project root:

```bash
uvicorn backend.main:app --reload --port 8000
```

Swagger UI will be at http://localhost:8000/docs

### Frontend

```bash
cd frontend
npm install
npm run dev
```

App runs at http://localhost:5173. All `/api` requests are proxied to the backend automatically.

## Project Structure

```
data-analyzer/
├── backend/
│   ├── main.py                 # FastAPI app, CORS setup
│   ├── session_store.py        # Shared in-memory dict: session_id -> RAG chain
│   ├── requirements.txt
│   ├── routers/
│   │   ├── health.py           # GET /health
│   │   ├── upload.py           # POST /api/upload/pdf and /api/upload/url
│   │   └── chat.py             # POST /api/chat
│   └── services/
│       ├── pdf_loader.py       # Loads and chunks PDFs using PyPDFLoader
│       ├── web_loader.py       # Fetches and chunks web pages using WebBaseLoader
│       └── rag_chain.py        # Builds FAISS vector store + RetrievalQA chain
│
└── frontend/src/
    ├── App.vue                 # Switches between the upload screen and chat
    ├── api.js                  # Axios calls to the backend
    ├── composables/
    │   ├── useUpload.js        # All upload state and logic in one place
    │   └── useChat.js          # Chat state, send message, scroll to bottom
    └── components/
        ├── layout/
        │   ├── AppHeader.vue
        │   └── AppFooter.vue
        ├── upload/
        │   ├── SourceSelector.vue  # The PDF / URL toggle cards
        │   ├── FileDropzone.vue    # Drag and drop area
        │   └── UrlSubmitter.vue    # URL input
        ├── chat/
        │   ├── ChatHeader.vue
        │   ├── ChatEmptyState.vue  # Shown before the first message
        │   ├── ChatMessage.vue
        │   ├── TypingIndicator.vue
        │   └── ChatInputBar.vue
        ├── DataInput.vue           # The full upload page
        └── ChatArea.vue            # The full chat page
```

## How it works

1. User uploads a PDF or submits a URL
2. Backend loads and splits the content into 1000-character chunks (200 overlap)
3. Each chunk is converted to a vector using OpenAI Embeddings
4. Vectors are stored in a FAISS index and saved in memory under a `session_id`
5. User asks a question in the chat
6. The question is embedded and the top 3 most similar chunks are retrieved from FAISS
7. Those chunks + the question are sent to GPT-4o-mini
8. The answer is returned to the frontend

## API Endpoints

| Method | Endpoint | What it does |
|--------|----------|--------------|
| GET | `/health` | Check if the server is running |
| POST | `/api/upload/pdf` | Upload a PDF, get back a session ID |
| POST | `/api/upload/url` | Submit a URL, get back a session ID |
| POST | `/api/chat` | Send a message, get an AI response |

## Author

Haseeb Ahmad (Fullstack Developer)

