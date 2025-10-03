# Learning Plan: Python & RAG for Java Developers

## Overview
This plan leverages the arxiv-paper-curator project to teach you Python development and RAG (Retrieval-Augmented Generation) concepts through hands-on experience. Coming from Java, you'll find many familiar concepts but with Python's more concise syntax and dynamic typing.

---

## Phase 1: Python Fundamentals (Week 1-2)

### 1.1 Java → Python Translation
**Goal:** Understand Python syntax through Java comparisons

**Key Differences to Learn:**
- **No types declarations** (dynamic typing vs Java's static typing)
- **Indentation matters** (replaces curly braces `{}`)
- **No semicolons** needed
- **Duck typing** vs interfaces
- **List comprehensions** vs streams
- **Decorators** vs annotations (but more powerful)
- **Context managers** (`with` statement) vs try-with-resources
- **Multiple inheritance** (Python supports it, Java doesn't)

**Study Files:**
```
src/config.py              # Settings class, environment variables (like Spring @Configuration)
src/models/paper.py        # ORM models (like JPA entities)
src/schemas/api/search.py  # Pydantic models (like Java records/DTOs with validation)
```

**Exercises:**
1. Compare `src/models/paper.py` (SQLAlchemy ORM) with JPA entities - notice similarities
2. Study `src/config.py` - see how Pydantic Settings replaces Spring @ConfigurationProperties
3. Rewrite a simple Java class in Python (e.g., create a Student class with methods)

### 1.2 Python Ecosystem & Tools
**Goal:** Master Python development workflow

**Learn:**
- **uv** - dependency management (like Maven/Gradle)
- **pytest** - testing (like JUnit)
- **FastAPI** - web framework (like Spring Boot)
- **Pydantic** - data validation (like Bean Validation but more powerful)
- **Type hints** - optional static typing (similar to Java types)

**Hands-on:**
```bash
# Set up environment
make start              # Start services (Docker Compose)
make test               # Run tests
make format            # Auto-format code
make lint              # Check code quality

# Direct commands
uv sync                # Install dependencies (like mvn install)
uv run pytest          # Run tests
uv run python -m src.main  # Run application
```

**Study Files:**
```
tests/unit/test_config.py              # Simple unit test
tests/api/routers/test_ping.py         # API testing (like MockMvc in Spring)
pyproject.toml                         # Dependencies (like pom.xml/build.gradle)
```

### 1.3 Async Programming
**Goal:** Understand async/await (Java's CompletableFuture on steroids)

**Concepts:**
- `async def` - declares async function
- `await` - waits for async operation
- Event loop - like Java's ExecutorService but built into language
- `asyncio` - standard library for async operations

**Study Files:**
```
src/routers/ask.py                    # Async API endpoints
src/services/opensearch/client.py    # Async operations with external service
src/dependencies.py                   # Dependency injection (like Spring @Autowired)
```

**Exercise:**
Write an async function that fetches data from an API using `httpx` (async HTTP client).

---

## Phase 2: Project Architecture Deep Dive (Week 3-4)

### 2.1 FastAPI Web Framework
**Goal:** Master modern Python web development

**Architecture Pattern:**
```
Routers (Controllers)
    ↓
Services (Business Logic)
    ↓
Repositories (Data Access)
    ↓
Models (Database Entities)
```

**Study Files (in order):**
```
1. src/main.py                          # Application entry point (like Spring @SpringBootApplication)
2. src/routers/ping.py                  # Simple health check endpoint
3. src/routers/hybrid_search.py         # Search API with business logic
4. src/dependencies.py                  # Dependency injection
5. src/repositories/paper.py            # Data access layer
```

**Key FastAPI Concepts:**
- **Path operations** - `@app.get()`, `@app.post()` (like `@GetMapping`, `@PostMapping`)
- **Dependency injection** - `Depends()` (like Spring DI)
- **Pydantic models** - Request/response validation (better than Bean Validation)
- **Lifespan events** - Startup/shutdown hooks (like `@PostConstruct`, `@PreDestroy`)
- **Automatic OpenAPI docs** - Available at `/docs` (Swagger UI built-in)

**Exercises:**
1. Create a new endpoint `GET /api/v1/papers/count` that returns total paper count
2. Add input validation using Pydantic models
3. Test it using the auto-generated docs at `http://localhost:8000/docs`

### 2.2 Database & ORM
**Goal:** Learn SQLAlchemy (Python's Hibernate)

**Study Files:**
```
src/models/paper.py                   # Entity model
src/repositories/paper.py             # Repository pattern
src/db/interfaces/postgresql.py      # Database connection
alembic/versions/                     # Database migrations (like Flyway/Liquibase)
```

**Key Concepts:**
- **SQLAlchemy ORM** - Maps Python classes to database tables
- **Session management** - Database transactions
- **Query API** - Fluent query builder (like Criteria API)
- **Alembic migrations** - Version control for database schema

**Exercise:**
1. Add a new field `read_count` to Paper model
2. Create migration with Alembic
3. Update repository to query papers by read_count

### 2.3 Dependency Injection & Factories
**Goal:** Understand Python's DI patterns

**Study Files:**
```
src/services/*/factory.py             # Factory pattern for services
src/dependencies.py                   # FastAPI dependency injection
src/main.py:lifespan()                # Application state management
```

**Key Differences from Java:**
- **Manual factories** instead of @Autowired (Python culture prefers explicit over implicit)
- **Function-based DI** using `Depends()`
- **Application state** in `app.state` (shared singletons)

**Exercise:**
Create a new service with factory pattern and inject it into an endpoint.

---

## Phase 3: RAG Fundamentals (Week 5-6)

### 3.1 What is RAG?
**Goal:** Understand RAG architecture and purpose

**RAG Pipeline:**
```
User Query
    ↓
1. Query Processing & Validation
    ↓
2. Document Retrieval (Search)
    ↓
3. Context Assembly (Top-K documents)
    ↓
4. Prompt Engineering (Query + Context → LLM)
    ↓
5. LLM Generation
    ↓
6. Response Streaming
```

**Why RAG?**
- LLMs have limited context window
- LLMs don't have real-time knowledge
- RAG grounds LLM responses in your data
- Reduces hallucinations

**Study Files:**
```
src/routers/ask.py                    # RAG endpoint implementation
src/services/ollama/prompts.py        # Prompt engineering
```

### 3.2 Search & Retrieval
**Goal:** Master hybrid search (keyword + semantic)

**Search Types:**
1. **BM25 (Keyword Search)** - Like traditional search, finds exact term matches
2. **Vector Search (Semantic)** - Uses embeddings to find similar meaning
3. **Hybrid Search** - Combines both using RRF (Reciprocal Rank Fusion)

**Study Files:**
```
src/services/opensearch/client.py         # OpenSearch operations
src/services/opensearch/query_builder.py  # BM25 + vector queries
src/services/embeddings/jina_client.py    # Convert text → vectors
```

**Concepts:**
- **Embeddings** - Numbers representing text meaning (768-dimensional vectors)
- **k-NN search** - Finding nearest vectors in high-dimensional space
- **BM25** - Probabilistic ranking algorithm
- **RRF** - Combines multiple ranking strategies

**Exercises:**
1. Test search modes: `curl http://localhost:8000/api/v1/search?q=transformer&mode=bm25`
2. Compare results between bm25, vector, and hybrid modes
3. Study how query_builder constructs different queries

### 3.3 Text Embeddings
**Goal:** Understand how text becomes vectors

**Study Files:**
```
src/services/embeddings/jina_client.py    # Jina AI embeddings API
src/schemas/embeddings/jina.py            # Embedding request/response models
```

**Concepts:**
- **Embedding models** - Neural networks trained to convert text → vectors
- **Dimensionality** - Jina uses 768-dimensional vectors
- **Similarity** - Cosine similarity measures how close vectors are
- **Batch processing** - Embed multiple texts at once for efficiency

**Exercise:**
1. Use Jina API to embed some sample texts
2. Calculate cosine similarity between embeddings
3. Verify similar texts have higher similarity scores

### 3.4 Document Processing & Chunking
**Goal:** Learn how PDFs become searchable chunks

**Study Files:**
```
src/services/pdf_parser/docling.py        # PDF → Markdown conversion
src/services/indexing/text_chunker.py     # Split text into chunks
src/services/indexing/hybrid_indexer.py   # Index chunks into OpenSearch
```

**Pipeline:**
```
PDF → Docling → Markdown → Chunking → Embeddings → Index
```

**Chunking Strategy:**
- **Chunk size**: 600 words
- **Overlap**: 100 words (prevents splitting related sentences)
- **Why?** LLMs have context limits, smaller chunks = more precise retrieval

**Exercise:**
Study `airflow/dags/arxiv_ingestion/` to see the full ingestion pipeline.

---

## Phase 4: Advanced RAG Concepts (Week 7-8)

### 4.1 LLM Integration
**Goal:** Learn to work with local LLMs using Ollama

**Study Files:**
```
src/services/ollama/client.py         # Ollama API client
src/services/ollama/prompts.py        # Prompt engineering
src/routers/ask.py                    # Streaming responses
```

**Key Concepts:**
- **Ollama** - Runs LLMs locally (like running ML models as microservices)
- **Prompt engineering** - Crafting effective prompts for best results
- **Streaming** - Token-by-token response (better UX)
- **Context window** - How much text LLM can process at once
- **Temperature** - Controls randomness (0 = deterministic, 1 = creative)

**RAG Prompt Structure:**
```
System: You are a helpful assistant...
User: [Context from search results]
      Question: [User's question]
```

**Exercise:**
1. Test different prompts in `src/services/ollama/prompts.py`
2. Experiment with temperature settings
3. Compare streaming vs non-streaming responses

### 4.2 Caching Layer
**Goal:** Optimize RAG with Redis caching

**Study Files:**
```
src/services/cache/client.py          # Redis cache implementation
src/routers/ask.py:64-82              # Cache-aside pattern
```

**Caching Strategy:**
- **Cache key** - Hash of (query + search_mode + model)
- **TTL** - 24 hours
- **Hit rate** - 60%+ in production
- **Performance** - 50ms cached vs 3-5s full RAG

**Pattern: Cache-Aside**
```python
# 1. Check cache
cached = cache.get(key)
if cached:
    return cached

# 2. Compute (expensive RAG operation)
result = rag_pipeline(query)

# 3. Store in cache
cache.set(key, result, ttl=86400)
return result
```

**Exercise:**
Add caching to the search endpoint.

### 4.3 Observability & Tracing
**Goal:** Monitor RAG performance with Langfuse

**Study Files:**
```
src/services/langfuse/tracer.py       # Tracing decorator
src/routers/ask.py                    # Traced endpoints
```

**Key Metrics:**
- **Search latency** - BM25: ~50ms, Hybrid: ~400ms
- **LLM generation time** - 3-5s for 300-word response
- **Cache hit rate** - 60%+
- **End-to-end latency** - User query → final response

**Langfuse Features:**
- Request/response logging
- Performance metrics
- Cost tracking (for paid APIs)
- Debug traces (see each RAG step)

**Exercise:**
1. Access Langfuse UI: `http://localhost:3000`
2. Make some RAG requests
3. Study the trace waterfall diagram

### 4.4 Data Pipeline with Airflow
**Goal:** Understand ETL for RAG data

**Study Files:**
```
airflow/dags/arxiv_paper_ingestion.py    # Main DAG
airflow/dags/arxiv_ingestion/fetching.py # Fetch from arXiv API
airflow/dags/arxiv_ingestion/indexing.py # Parse PDF & index
```

**Pipeline Steps:**
```
1. Fetch - Download papers from arXiv API
2. Parse - Extract text from PDFs using Docling
3. Chunk - Split into 600-word chunks with 100-word overlap
4. Embed - Generate vector embeddings using Jina
5. Index - Store in OpenSearch for hybrid search
6. Report - Log success/failure metrics
```

**Airflow Concepts:**
- **DAG** - Directed Acyclic Graph of tasks
- **Tasks** - Individual operations
- **Dependencies** - Task execution order
- **Schedule** - When to run pipeline (e.g., daily)

**Exercise:**
1. Access Airflow UI: `http://localhost:8080`
2. Trigger the arxiv_paper_ingestion DAG manually
3. Monitor task execution and logs

---

## Phase 5: Hands-On Projects (Week 9-12)

### Project Ideas (Increasing Complexity)

#### 5.1 Beginner: Add New Endpoint
**Goal:** Create `/api/v1/papers/recent` endpoint

Requirements:
- Return papers from last 7 days
- Support pagination
- Add Pydantic response model
- Write tests

**Files to create/modify:**
- `src/routers/papers.py` (new)
- `src/repositories/paper.py` (modify)
- `tests/api/routers/test_papers.py` (new)

#### 5.2 Intermediate: Implement Bookmarking
**Goal:** Let users bookmark favorite papers

Requirements:
- Add `bookmarked` boolean field to Paper model
- Create migration
- Add `POST /api/v1/papers/{id}/bookmark` endpoint
- Add `GET /api/v1/papers/bookmarked` to list bookmarks
- Write tests

**Skills practiced:**
- Database migrations
- ORM relationships
- REST API design
- Testing

#### 5.3 Advanced: Add Query Rewriting
**Goal:** Improve search with query expansion

Requirements:
- Use LLM to rewrite user queries (expand acronyms, add synonyms)
- Example: "LLM" → "Large Language Model OR LLM OR language models"
- Cache rewritten queries
- Compare search quality before/after
- Add metrics to Langfuse

**Skills practiced:**
- LLM prompting
- Caching strategies
- Performance optimization
- A/B testing

#### 5.4 Expert: Build Citation Graph
**Goal:** Extract and visualize paper citations

Requirements:
- Parse citations from PDF references section
- Build citation graph (papers citing each other)
- Create API endpoint to get related papers
- Visualize with network graph (use Gradio or FastAPI + frontend)

**Skills practiced:**
- Text extraction
- Graph algorithms
- API design
- Data visualization

---

## Study Resources

### Python Learning
- **Official Tutorial**: https://docs.python.org/3/tutorial/
- **Real Python**: https://realpython.com/ (Java → Python guide)
- **Fluent Python**: Book for intermediate/advanced (highly recommended)

### FastAPI
- **Official Docs**: https://fastapi.tiangolo.com/
- **Tutorial**: https://fastapi.tiangolo.com/tutorial/

### RAG Concepts
- **RAG Paper**: https://arxiv.org/abs/2005.11401 (original research)
- **Vector Search**: https://www.pinecone.io/learn/vector-search/
- **Prompt Engineering**: https://www.promptingguide.ai/

### This Project's Architecture
- **CLAUDE.md**: Development commands and architecture overview
- **README.md**: Project setup and deployment
- **Docker Compose**: `docker-compose.yml` for services

---

## Weekly Study Schedule

### Week 1-2: Python Fundamentals
- **Mon/Wed/Fri**: 2 hours coding exercises (Python basics)
- **Tue/Thu**: 1 hour reading (Python docs, comparing to Java)
- **Weekend**: 3 hours project setup + run through codebase

### Week 3-4: FastAPI & Architecture
- **Mon/Wed/Fri**: 2 hours implementing new endpoints
- **Tue/Thu**: 1 hour studying existing code
- **Weekend**: Build mini-project (simple REST API)

### Week 5-6: RAG Fundamentals
- **Mon/Wed/Fri**: 2 hours studying search & embeddings
- **Tue/Thu**: 1 hour experimenting with Ollama/Jina APIs
- **Weekend**: Build simple RAG (no caching/optimization)

### Week 7-8: Advanced RAG
- **Mon/Wed/Fri**: 2 hours adding caching, tracing, optimization
- **Tue/Thu**: 1 hour reading RAG papers/blogs
- **Weekend**: Performance testing & monitoring

### Week 9-12: Projects
- **Mon-Fri**: 2 hours/day on chosen project
- **Weekend**: Code review, testing, documentation

---

## Daily Development Workflow

```bash
# 1. Start services
make start

# 2. Check health
make health
curl http://localhost:8000/health

# 3. Develop (TDD approach)
uv run pytest tests/test_my_feature.py  # Write test first (red)
# ... implement feature ...
uv run pytest tests/test_my_feature.py  # Make it pass (green)
make format                              # Format code
make lint                                # Check quality

# 4. Manual testing
# Access API docs: http://localhost:8000/docs
# Test endpoints interactively

# 5. Commit
git add .
git commit -m "Add feature X"

# 6. Stop services
make stop
```

---

## Common Pitfalls (Java → Python)

### 1. Indentation
```python
# ❌ Wrong
def foo():
print("hello")  # IndentationError

# ✅ Correct
def foo():
    print("hello")  # 4 spaces
```

### 2. Mutability
```python
# ❌ Dangerous: default mutable argument
def add_item(item, items=[]):  # Same list reused across calls!
    items.append(item)
    return items

# ✅ Correct
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

### 3. None vs null
```python
# Java: if (obj != null)
# Python: if obj is not None:

# ✅ Correct
if value is not None:
    process(value)
```

### 4. String Formatting
```python
# Java: String.format("Hello %s", name)
# Python: Multiple ways

name = "Alice"
f"Hello {name}"           # ✅ F-strings (modern, preferred)
"Hello {}".format(name)   # ✅ .format()
"Hello %s" % name         # ❌ Old style (avoid)
```

### 5. List Comprehensions vs Loops
```python
# Java style (verbose)
result = []
for i in range(10):
    if i % 2 == 0:
        result.append(i * 2)

# ✅ Python style (idiomatic)
result = [i * 2 for i in range(10) if i % 2 == 0]
```

---

## Quick Reference: Java → Python

| Java | Python | Notes |
|------|--------|-------|
| `public class Foo` | `class Foo:` | No access modifiers |
| `String name;` | `name: str` | Type hints optional |
| `List<String>` | `list[str]` | Lowercase `list` |
| `Map<String, Integer>` | `dict[str, int]` | Called `dict` |
| `null` | `None` | Use `is None` not `== None` |
| `@Override` | No decorator needed | Duck typing |
| `interface IFoo` | `class Foo(Protocol):` | Python 3.8+ |
| `try-with-resources` | `with` statement | Context managers |
| `@Autowired` | `Depends()` | FastAPI DI |
| `Optional<T>` | `T \| None` or `Optional[T]` | Python 3.10+ |

---

## Testing Strategy

### Unit Tests
```python
# tests/unit/services/test_my_service.py
def test_parse_query():
    result = parse_query("transformers in NLP")
    assert result.terms == ["transformers", "nlp"]
    assert result.field == "abstract"
```

### Integration Tests
```python
# tests/integration/test_search.py
@pytest.mark.integration
async def test_opensearch_connection(opensearch_client):
    result = await opensearch_client.search("test query")
    assert result.total > 0
```

### API Tests
```python
# tests/api/routers/test_my_router.py
async def test_search_endpoint(client):
    response = await client.get("/api/v1/search?q=transformers")
    assert response.status_code == 200
    data = response.json()
    assert "results" in data
```

---

## Success Metrics

By the end of this plan, you should be able to:

**Python Skills:**
- [ ] Write idiomatic Python code (not Java-style Python)
- [ ] Use type hints effectively
- [ ] Understand async/await
- [ ] Build REST APIs with FastAPI
- [ ] Write tests with pytest
- [ ] Use Python packaging tools (uv)

**RAG Skills:**
- [ ] Explain RAG architecture and benefits
- [ ] Implement hybrid search (BM25 + vector)
- [ ] Work with embeddings and vector databases
- [ ] Integrate LLMs into applications
- [ ] Design effective prompts
- [ ] Optimize RAG with caching
- [ ] Monitor RAG performance

**Project Skills:**
- [ ] Navigate and understand this codebase
- [ ] Add new features independently
- [ ] Write tests for new code
- [ ] Debug issues using logs/traces
- [ ] Optimize performance bottlenecks

---

## Getting Help

1. **Code Questions**: Read file docstrings and comments
2. **Python Syntax**: https://docs.python.org/3/
3. **FastAPI**: https://fastapi.tiangolo.com/
4. **RAG Concepts**: Re-read Phase 3-4 sections
5. **Debugging**: Check Langfuse traces at http://localhost:3000

---

## Next Steps

1. ✅ Read this entire document
2. Set up development environment: `make start`
3. Verify services: `make health`
4. Start Phase 1: Study `src/config.py` and compare to Java
5. Run first test: `uv run pytest tests/unit/test_config.py -v`
6. Ask questions and experiment!

**Remember:** The best way to learn is by doing. Don't just read code—modify it, break it, fix it, and understand why it works.

Good luck! 🚀
