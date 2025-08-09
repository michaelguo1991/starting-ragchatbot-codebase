# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Architecture Overview

A Retrieval-Augmented Generation (RAG) system for course materials with the following architecture:

```
Frontend (Vanilla JS) → FastAPI → RAGSystem → DocumentProcessor → VectorStore → Claude AI
                                      ↓
                              SearchTools (Course/Lesson search)
```

## Key Components

### Core Classes
- **RAGSystem** (`backend/rag_system.py`): Main orchestrator managing document processing, vector storage, AI generation, and session management
- **DocumentProcessor** (`backend/document_processor.py`): Handles PDF/DOCX/TXT parsing, chunking, and metadata extraction
- **VectorStore** (`backend/vector_store.py`): ChromaDB integration with semantic search, course/lesson filtering, and metadata storage
- **AIGenerator** (`backend/ai_generator.py`): Claude Sonnet 4 integration with tool-based search capabilities
- **SearchTools** (`backend/search_tools.py`): Structured search tools for course materials with fuzzy matching

### Data Models
- **Course**: High-level course metadata (title, instructor, lessons)
- **Lesson**: Individual lesson within a course
- **CourseChunk**: Text chunk with associated metadata for vector storage

## Development Commands

### Setup & Installation
```bash
# Install dependencies
uv sync

# Set environment variables
echo "ANTHROPIC_API_KEY=your_key" > .env
```

### Running the Application
```bash
# Quick start (recommended)
chmod +x run.sh && ./run.sh

# Manual development server
cd backend && uv run uvicorn app:app --reload --port 8000
```

### API Endpoints
- `POST /api/query` - Main query endpoint with session support
- `GET /api/courses` - Course statistics and listing

### Testing the System
1. Start the server (see commands above)
2. Visit http://localhost:8000 for web interface
3. Ask questions like "What is covered in lesson 5 of the MCP course?"

## Key Usage Patterns

### Adding New Course Materials
- Place files in `docs/` directory (PDF/DOCX/TXT)
- Restart server for auto-ingestion
- System automatically chunks and embeds content

### Query Processing Flow
1. User query received via `/api/query`
2. RAGSystem.query() orchestrates processing
3. SearchTools performs semantic search with course/lesson filtering
4. AI generates response using retrieved context
5. Conversation history maintained via session_id

### Search Capabilities
- **Semantic search**: Vector similarity across course content
- **Course filtering**: Search within specific courses
- **Lesson targeting**: Query specific lessons by number/title
- **Fuzzy matching**: Handles variations in course names

## File Structure
```
backend/
├── app.py              # FastAPI routes and startup
├── rag_system.py       # Main orchestrator
├── document_processor.py # Document parsing & chunking
├── vector_store.py     # ChromaDB operations
├── ai_generator.py     # Claude integration
├── search_tools.py     # Search utilities
└── models.py          # Data structures

frontend/
├── index.html         # Chat interface
├── script.js          # API client & UI logic
└── style.css          # Styling
docs/                  # Course materials (auto-ingested)
```

## Configuration
Key settings in `backend/config.py`:
- `CHUNK_SIZE`: 800 characters
- `CHUNK_OVERLAP`: 100 characters
- `MAX_RESULTS`: 5 search results
- `EMBEDDING_MODEL`: all-MiniLM-L6-v2
- `ANTHROPIC_MODEL`: claude-sonnet-4-20250514