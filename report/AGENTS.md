# AI-Powered Semantic Search for Umbraco CMS - Project Summary

This document is an overview of the project. The main content of this folder is the projet report, written in latex. The written language is danish, and therefore, most responses must be in danish as well.

## Project Overview

Bachelor engineering project implementing AI-driven semantic search for an Umbraco-based content platform serving multiple user groups (e.g., farmers, veterinarians). The system uses locally hosted AI models (Ollama) for cost-effective, self-hosted semantic search with user group personalization.

## Core Objectives

- Enable natural language search queries that understand meaning, not just keywords
- Personalize search results based on user group membership
- Provide semantic answer generation (RAG) for queries
- Deploy self-hosted AI infrastructure to avoid cloud API dependencies and costs

## System Architecture

### Existing Infrastructure

- **Backend**: Umbraco CMS (.NET/ASP.NET Core) with MSSQL database
- **Frontend**: Next.js application
- **Orchestration**: .NET Aspire for managing all services

### New Components

- **AI Microservice**: Python FastAPI container handling embedding generation and answer generation
- **Ollama Container**: Hosts AI models (embeddings + LLM)
- **Vector Storage**: Embeddings stored in existing MSSQL database (cost constraint - no dedicated vector DB budget)

### Architecture Flow

```
User → Next.js Frontend → Umbraco Backend (orchestrator) → AI Microservice → Ollama
                                ↓
                          MSSQL (content + embeddings)
```

## Technical Decisions

### AI Models

- **Embeddings**: `mxbai-embed-large` is used for now. `nomic-embed-text` offered too poor danish support. Other models to be maybe tested.
- **Answer Generation**: `llama3.2:3b` used for now. Model must have danish support and a large enough context window for pdfs and context.
- **Language Consideration**: Danish content requires multilingual model; initial tests show English-focused models underperform

### Content Processing Strategy

- **Text Extraction**: Backend (Umbraco) pre-processes all content to plain text before sending to AI service
- **Umbraco Content**: Extract from blocks, rich text, nested content using custom .NET extractor
- **PDFs**: Extract text using PdfPig or iText7 library in .NET backend. TBD.
- **Chunking**: Long documents split into ~500-token chunks with 50-token overlap for optimal embedding quality

### Data Architecture

- **AI Microservice stores**: Content ID + Embedding vector only (no text duplication). Maybe some more data required in the future?
- **Umbraco Backend maintains**: Full content as source of truth, orchestrates all workflows
- **MSSQL Schema**: ContentEmbeddings table with ContentId, ChunkIndex, ChunkText, Embedding (VARBINARY), metadata. Needs to be handled withing Python AI microservice.

### Why Backend Preprocessing (not AI service)?

- Umbraco knows content structure best
- Avoids data duplication and sync issues
- Cleaner separation of concerns
- AI service stays simple and focused on embeddings/generation

## Key Features (Epics)

### Epic 1: Semantic Search

- Natural language queries find content based on meaning
- Automatic embedding generation when content published/updated
- Guest users get non-personalized results; logged-in users see all results

### Epic 2: Personalized Search

- Results ranked/filtered based on user group (farmer, veterinarian, etc.)
- Backend identifies user group, passes to AI service for context-aware ranking
- High-relevance content for user's group appears first

### Epic 3: Semantic Answer (RAG)

- Retrieval-Augmented Generation: retrieve top 3-5 relevant chunks, generate summary answer
- Backend fetches full text for top results, sends to AI service with query
- LLM generates concise answer (150-200 words) with source attribution
- Progressive loading: show search results immediately (~500ms), stream answer later (2-5s)

## Performance Considerations

- **Semantic Search**: ~500ms (acceptable)
- **With Answer Generation**: 3-6 seconds (managed via progressive UX and caching)
- **Context Limits**: Use 2-4k tokens for RAG (despite 128k model capability) for speed and quality
- **Optimization**: Cache common queries, parallel processing, progressive response streaming

## Testing & Quality

- Golden dataset approach: predefined query-result pairs for validation
- Comparative testing: new semantic search vs. old keyword search (Examine)
- User group differentiation tests: verify different results per role
- Manual evaluation for 10-20 representative queries per user group
- Precision@K metrics for academic rigor

## Implementation Priorities

- **Must Have**: Embedding generation, basic semantic search, user group filtering, Ollama deployment
- **Should Have**: Automatic re-indexing, performance optimization, answer generation
- **Could Have**: Query enhancement with LLM, search analytics
- **Won't Have**: Custom model training, real-time collaboration features

## Key Challenges Identified

1. **Language Support**: Danish content requires careful model selection (multilingual models preferred)
2. **Vector Search in SQL**: MSSQL lacks specialized vector indexes; performance acceptable for proof-of-concept scale (~1000 documents)
3. **RAG Latency**: Answer generation adds 2-5 seconds; addressed with progressive UX
4. **Content Chunking**: Long PDFs need intelligent chunking to fit context windows and maintain quality

## Technology Stack Summary

- **Orchestration**: .NET Aspire (unified service management)
- **Backend**: Umbraco/ASP.NET Core, MSSQL
- **AI API Framework**: Python FastAPI (better AI ecosystem than .NET for this use case)
- **AI Models**: Ollama (local hosting)
- **Frontend**: Next.js
- **PDF Processing**: TBD

## User Stories Structure

Organized into user-facing epics (semantic search, personalized search, semantic answer) with acceptance criteria focused on observable outcomes. Technical infrastructure work treated as tasks or enablers under user stories, maintaining user-centric approach while acknowledging technical reality.

## Project Scope

Proof-of-concept for ~100 content pages demonstrating AI-powered search with personalization. Focus is on building/deploying rather than research. Evaluation combines automated tests, golden datasets, comparative analysis, and manual evaluation to handle subjective nature of search relevance.

## RAG (Retrieval-Augmented Generation) Implementation

- Backend orchestrates entire RAG flow
- Semantic search identifies top results by ID
- Backend fetches full text from Umbraco for top 3-5 results
- Text sent to AI service with query for answer generation
- No text duplication in AI service - Umbraco remains single source of truth
- Progressive response: search results delivered first, generated answer streams after

## Model Specifications Understanding

- **Size**: Disk/RAM requirements (keep under 6GB for 8GB RAM systems)
- **Context**: Max tokens per request (128k = ~96k words, but use only 2-4k for optimal RAG performance)
- **Input**: Text input required for this project
- **Recommendation**: llama3.2:3b (2GB, 128k context) for answer generation alongside nomic-embed-text for embeddings
