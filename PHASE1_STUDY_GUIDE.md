# Phase 1 Study Guide: Python Fundamentals for Java Developers

## Overview
This detailed study guide uses concrete code examples from the arXiv Paper Curator project to teach Python fundamentals to Java developers. Each concept is explained with direct comparisons to Java and includes hands-on exercises.

---

## 1. Python vs Java: Syntax and Basic Structure

### 1.1 File Structure and Imports

**Java Structure:**
```java
// Java: Config.java
package com.example.config;

import java.util.Map;
import java.util.HashMap;
import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties(prefix = "arxiv")
public class ArxivConfig {
    private String baseUrl = "https://export.arxiv.org/api/query";
    private Map<String, String> namespaces = new HashMap<>();

    // getters and setters...
}
```

**Python Equivalent (from `src/config.py`):**
```python
# Python: No package declaration needed
import os
from pathlib import Path
from typing import Literal

from pydantic import Field, field_validator
from pydantic_settings import BaseSettings, SettingsConfigDict

class ArxivSettings(BaseSettings):
    base_url: str = "https://export.arxiv.org/api/query"
    namespaces: dict = {
        "atom": "http://www.w3.org/2005/Atom",
        "opensearch": "http://a9.com/-/spec/opensearch/1.1/",
        "arxiv": "http://arxiv.org/schemas/atom",
    }
```

**Key Differences:**
- **No package declarations**: Directory structure determines module path
- **Type hints**: Optional but recommended (`:str`, `:dict`)
- **Default values**: Assigned directly in class definition
- **Imports**: More granular, can import specific functions/classes

### 1.2 Variables and Dynamic Typing

**Java:**
```java
// Java: Explicit type declarations required
String baseUrl = "https://export.arxiv.org/api/query";
int maxResults = 15;
Map<String, String> namespaces = new HashMap<>();
```

**Python (from `src/config.py`):**
```python
# Python: Dynamic typing, optional type hints
base_url: str = "https://export.arxiv.org/api/query"  # Type hint + default
max_results: int = 15
namespaces: dict = {}  # Type hint for clarity

# Or without type hints (still valid)
base_url = "https://export.arxiv.org/api/query"
max_results = 15
namespaces = {}
```

**Key Concepts:**
- **Dynamic typing**: Variables can change type at runtime
- **Type hints**: Documentation and IDE support, not enforced at runtime
- **Duck typing**: "If it walks like a duck and quacks like a duck, it's a duck"

---

## 2. Data Structures: Python vs Java

### 2.1 Lists vs ArrayList

**Java:**
```java
// Java: ArrayList
List<String> authors = new ArrayList<>();
authors.add("John Doe");
authors.add("Jane Smith");
String firstAuthor = authors.get(0);
int size = authors.size();
```

**Python (from `src/models/paper.py`):**
```python
# Python: Built-in list
authors = ["John Doe", "Jane Smith"]  # List literal
authors.append("Bob Wilson")          # Add element
first_author = authors[0]             # Index access
size = len(authors)                   # Built-in len() function

# List comprehension (powerful Python feature)
author_names = [author.upper() for author in authors]
```

### 2.2 Dictionaries vs HashMap

**Java:**
```java
// Java: HashMap
Map<String, String> namespaces = new HashMap<>();
namespaces.put("atom", "http://www.w3.org/2005/Atom");
namespaces.put("arxiv", "http://arxiv.org/schemas/atom");
String atomUrl = namespaces.get("atom");
```

**Python (from `src/config.py`):**
```python
# Python: Built-in dict
namespaces = {
    "atom": "http://www.w3.org/2005/Atom",
    "arxiv": "http://arxiv.org/schemas/atom"
}
atom_url = namespaces["atom"]        # Direct access
atom_url = namespaces.get("atom")    # Safe access (returns None if missing)

# Dictionary comprehension
upper_keys = {k.upper(): v for k, v in namespaces.items()}
```

### 2.3 JSON Handling

**Python advantage (from `src/schemas/api/search.py`):**
```python
# Python: Native JSON-like syntax
search_config = {
    "query": "machine learning",
    "size": 10,
    "categories": ["cs.AI", "cs.LG"],
    "latest_papers": False,
    "use_hybrid": True
}

# Easy nested access
if search_config["use_hybrid"]:
    print(f"Searching for: {search_config['query']}")
```

---

## 3. Object-Oriented Programming: Classes and Properties

### 3.1 Basic Class Definition

**Java:**
```java
// Java: Verbose class definition
public class Paper {
    private String arxivId;
    private String title;
    private List<String> authors;
    private Date publishedDate;

    public Paper(String arxivId, String title) {
        this.arxivId = arxivId;
        this.title = title;
    }

    public String getArxivId() { return arxivId; }
    public void setArxivId(String arxivId) { this.arxivId = arxivId; }
    // ... more getters/setters
}
```

**Python (from `src/models/paper.py`):**
```python
# Python: Concise class definition
import uuid
from datetime import datetime, timezone
from sqlalchemy import Column, String, Text, JSON, DateTime, Boolean

class Paper(Base):
    __tablename__ = "papers"

    # Class attributes (like static fields)
    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    arxiv_id = Column(String, unique=True, nullable=False, index=True)
    title = Column(String, nullable=False)
    authors = Column(JSON, nullable=False)
    published_date = Column(DateTime, nullable=False)

    # Automatic timestamps
    created_at = Column(DateTime, default=lambda: datetime.now(timezone.utc))
    updated_at = Column(DateTime,
                       default=lambda: datetime.now(timezone.utc),
                       onupdate=lambda: datetime.now(timezone.utc))
```

**Key Python Features:**
- **No explicit getters/setters**: Direct attribute access
- **Special methods**: `__tablename__` (like static fields)
- **Lambda functions**: `lambda: datetime.now(timezone.utc)`
- **Method chaining**: Common pattern for fluent APIs

### 3.2 Properties and Data Validation

**Python Properties (from `src/services/arxiv/client.py`):**
```python
class ArxivClient:
    def __init__(self, settings: ArxivSettings):
        self._settings = settings  # Private convention (underscore prefix)

    @property
    def base_url(self) -> str:
        """Property acts like a getter method."""
        return self._settings.base_url

    @cached_property
    def pdf_cache_dir(self) -> Path:
        """Computed once, then cached."""
        cache_dir = Path(self._settings.pdf_cache_dir)
        cache_dir.mkdir(parents=True, exist_ok=True)
        return cache_dir
```

**Pydantic Validation (from `src/config.py`):**
```python
class Settings(BaseSettings):
    # Field validation with constraints
    app_version: str = "0.1.0"
    environment: Literal["development", "staging", "production"] = "development"
    postgres_database_url: str = "postgresql://..."

    @field_validator("postgres_database_url")
    @classmethod
    def validate_database_url(cls, v: str) -> str:
        """Custom validation method."""
        if not (v.startswith("postgresql://") or v.startswith("postgresql+psycopg2://")):
            raise ValueError("Database URL must start with 'postgresql://'")
        return v
```

**Java Equivalent:**
```java
// Java: Bean Validation
@Entity
public class Settings {
    @Pattern(regexp = "^postgresql://.*")
    private String postgresDatabaseUrl;

    @Valid
    private ArxivSettings arxiv;
}
```

---

## 4. Functions and Methods

### 4.1 Function Definition and Parameters

**Python (from `src/dependencies.py`):**
```python
from typing import Annotated, Generator
from fastapi import Depends

# Simple function
def get_settings() -> Settings:
    """Get application settings."""
    return Settings()

# Function with default parameters
def get_db_session(database: Annotated[BaseDatabase, Depends(get_database)]) -> Generator[Session, None, None]:
    """Get database session dependency."""
    with database.get_session() as session:
        yield session

# Lambda function (anonymous function)
create_timestamp = lambda: datetime.now(timezone.utc)
```

**Advanced Function Features:**
```python
# Keyword arguments
def search_papers(query: str, size: int = 10, categories: List[str] = None):
    if categories is None:
        categories = []
    return f"Searching '{query}' in {categories}, limit {size}"

# Call with keyword arguments
results = search_papers("AI", size=20, categories=["cs.AI"])
results = search_papers("AI", categories=["cs.AI"])  # size uses default
```

### 4.2 Decorators (Python's Powerful Feature)

**Caching Decorator (from `src/dependencies.py`):**
```python
from functools import lru_cache

@lru_cache  # Decorator caches function results
def get_settings() -> Settings:
    """Get application settings - cached after first call."""
    return Settings()
```

**Validation Decorator (from `src/config.py`):**
```python
@field_validator("pdf_cache_dir")
@classmethod
def validate_cache_dir(cls, v: str) -> str:
    """Decorator for field validation."""
    os.makedirs(v, exist_ok=True)  # Create directory if it doesn't exist
    return v
```

**Property Decorator:**
```python
class ArxivClient:
    @property
    def base_url(self) -> str:
        return self._settings.base_url

    @cached_property  # Computed once, cached thereafter
    def pdf_cache_dir(self) -> Path:
        return Path(self._settings.pdf_cache_dir)
```

---

## 5. Async/Await Programming

### 5.1 Async Functions vs Java CompletableFuture

**Java (CompletableFuture):**
```java
// Java: Complex async programming
public CompletableFuture<List<Paper>> fetchPapersAsync(int maxResults) {
    return CompletableFuture.supplyAsync(() -> {
        // HTTP request logic
        return paperList;
    }).thenCompose(papers -> {
        // Process papers
        return CompletableFuture.completedFuture(papers);
    });
}
```

**Python (from `src/services/arxiv/client.py`):**
```python
import asyncio
import httpx

class ArxivClient:
    async def fetch_papers(
        self,
        max_results: Optional[int] = None,
        start: int = 0,
        sort_by: str = "submittedDate"
    ) -> List[ArxivPaper]:
        """Async function - returns a coroutine."""

        # Rate limiting
        await self._enforce_rate_limit()

        # Async HTTP request
        async with httpx.AsyncClient() as client:
            response = await client.get(url, timeout=self.timeout_seconds)

        return await self._parse_response(response.text)

    async def _enforce_rate_limit(self):
        """Private async method."""
        if self._last_request_time is not None:
            elapsed = time.time() - self._last_request_time
            if elapsed < self.rate_limit_delay:
                await asyncio.sleep(self.rate_limit_delay - elapsed)
        self._last_request_time = time.time()
```

**FastAPI Endpoint (from `src/routers/ping.py`):**
```python
from fastapi import APIRouter

@router.get("/health")
async def health_check(settings: SettingsDep, database: DatabaseDep) -> HealthResponse:
    """Async endpoint handler."""

    # Mix sync and async operations
    def _check_database():
        with database.get_session() as session:
            session.execute(text("SELECT 1"))
        return ServiceStatus(status="healthy")

    # Async call
    ollama_health = await ollama_client.health_check()

    return HealthResponse(status="ok", services=services)
```

### 5.2 Context Managers and Async Context

**Sync Context Manager:**
```python
# Python: 'with' statement (like try-with-resources)
def get_db_session(database):
    with database.get_session() as session:  # Automatic cleanup
        yield session
    # Session automatically closed here
```

**Async Context Manager (from `src/main.py`):**
```python
from contextlib import asynccontextmanager

@asynccontextmanager
async def lifespan(app: FastAPI):
    """Startup and shutdown logic."""
    # Startup
    logger.info("Starting RAG API...")
    database = make_database()
    app.state.database = database

    yield  # Application runs here

    # Shutdown
    database.teardown()
    logger.info("API shutdown complete")

# Usage in FastAPI
app = FastAPI(lifespan=lifespan)
```

---

## 6. Exception Handling

### 6.1 Exception Hierarchy

**Python (from `src/exceptions.py`):**
```python
# Base exception classes
class RepositoryException(Exception):
    """Base exception for repository-related errors."""

class PaperNotFound(RepositoryException):
    """Exception raised when paper data is not found."""

class PaperNotSaved(RepositoryException):
    """Exception raised when paper data is not saved."""

# Usage in code
def find_paper(arxiv_id: str) -> Paper:
    paper = database.query(Paper).filter(Paper.arxiv_id == arxiv_id).first()
    if not paper:
        raise PaperNotFound(f"Paper with arXiv ID {arxiv_id} not found")
    return paper
```

### 6.2 Exception Handling Patterns

**Try-Except vs Try-Catch:**
```python
# Python exception handling
async def fetch_with_retry(url: str, max_retries: int = 3):
    for attempt in range(max_retries):
        try:
            async with httpx.AsyncClient() as client:
                response = await client.get(url, timeout=30)
                return response.json()
        except httpx.TimeoutException:
            logger.warning(f"Timeout on attempt {attempt + 1}")
            if attempt == max_retries - 1:
                raise ArxivAPITimeoutError(f"Failed after {max_retries} attempts")
        except httpx.HTTPStatusError as e:
            logger.error(f"HTTP error: {e.response.status_code}")
            raise ArxivAPIException(f"HTTP error: {e}")
        except Exception as e:
            logger.error(f"Unexpected error: {e}")
            raise
```

---

## 7. Modules and Package Structure

### 7.1 Import System

**Project Structure:**
```
src/
├── config.py              # Configuration classes
├── models/
│   └── paper.py           # Database models
├── schemas/
│   └── api/
│       └── search.py      # API schemas
├── services/
│   ├── arxiv/
│   │   └── client.py      # ArXiv service
│   └── cache/
│       └── client.py      # Cache service
└── dependencies.py        # Dependency injection
```

**Import Examples:**
```python
# Absolute imports (recommended)
from src.config import Settings, ArxivSettings
from src.models.paper import Paper
from src.schemas.api.search import SearchRequest, SearchResponse
from src.services.arxiv.client import ArxivClient

# Relative imports (within same package)
from .client import ArxivClient        # Same directory
from ..cache.client import CacheClient # Parent directory
from ...models.paper import Paper      # Two levels up

# Import specific items
from typing import List, Optional, Dict, Literal
from datetime import datetime, timezone
from pathlib import Path
```

### 7.2 Package Initialization

**`__init__.py` files:**
```python
# src/services/__init__.py
"""Services package for external integrations."""

# src/services/arxiv/__init__.py
from .client import ArxivClient
from .factory import make_arxiv_client

__all__ = ["ArxivClient", "make_arxiv_client"]
```

---

## 8. Type System and Validation

### 8.1 Type Hints vs Java Types

**Python Type Hints (from `src/schemas/api/search.py`):**
```python
from typing import List, Optional, Dict, Any, Union
from pydantic import BaseModel, Field

class SearchRequest(BaseModel):
    """Request model with type validation."""

    query: str = Field(..., min_length=1, max_length=500)  # Required field
    size: int = Field(default=10, ge=1, le=50)             # With constraints
    categories: Optional[List[str]] = Field(default=None)   # Optional list
    latest_papers: bool = Field(default=False)              # Boolean with default

    # Complex types
    metadata: Dict[str, Any] = Field(default_factory=dict)  # Dictionary
    filters: Union[str, List[str]] = None                   # Union type
```

**Runtime Validation with Pydantic:**
```python
# Automatic validation and conversion
try:
    request = SearchRequest(
        query="machine learning",
        size="20",  # String automatically converted to int
        categories=["cs.AI"],
        latest_papers="true"  # String converted to bool
    )
    print(f"Valid request: {request.query}")
except ValidationError as e:
    print(f"Validation error: {e}")
```

### 8.2 Advanced Type Features

**Generic Types and Protocols:**
```python
from typing import TypeVar, Generic, Protocol
from abc import ABC, abstractmethod

T = TypeVar('T')

class Repository(Generic[T], ABC):
    """Generic repository interface."""

    @abstractmethod
    async def save(self, entity: T) -> T:
        pass

    @abstractmethod
    async def find_by_id(self, id: str) -> Optional[T]:
        pass

class PaperRepository(Repository[Paper]):
    """Concrete implementation for Paper entities."""

    async def save(self, paper: Paper) -> Paper:
        # Implementation
        pass
```

---

## 9. Practical Examples from the Codebase

### 9.1 Dependency Injection Pattern

**FastAPI Dependency System (from `src/dependencies.py`):**
```python
from functools import lru_cache
from typing import Annotated
from fastapi import Depends

# Singleton pattern with caching
@lru_cache
def get_settings() -> Settings:
    return Settings()

# Database session management
def get_db_session(database: Annotated[BaseDatabase, Depends(get_database)]):
    with database.get_session() as session:
        yield session  # Generator function

# Type aliases for cleaner code
SettingsDep = Annotated[Settings, Depends(get_settings)]
SessionDep = Annotated[Session, Depends(get_db_session)]

# Usage in endpoint
@router.get("/papers")
async def list_papers(
    settings: SettingsDep,          # Automatic injection
    session: SessionDep,            # Database session
    size: int = 10
) -> List[Paper]:
    return session.query(Paper).limit(size).all()
```

### 9.2 Configuration Management

**Environment-based Configuration (from `src/config.py`):**
```python
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    # Automatic environment variable loading
    app_version: str = "0.1.0"                    # From APP_VERSION
    debug: bool = True                            # From DEBUG
    postgres_database_url: str = "postgresql://..." # From POSTGRES_DATABASE_URL

    # Nested configuration with prefixes
    arxiv: ArxivSettings = Field(default_factory=ArxivSettings)  # ARXIV__*
    redis: RedisSettings = Field(default_factory=RedisSettings)  # REDIS__*

    model_config = SettingsConfigDict(
        env_file=".env",           # Load from .env file
        case_sensitive=False,      # Case insensitive env vars
        env_nested_delimiter="__"  # Support REDIS__HOST format
    )
```

### 9.3 Async HTTP Client Pattern

**Modern HTTP Client (from `src/services/arxiv/client.py`):**
```python
import httpx
import asyncio

class ArxivClient:
    async def fetch_papers(self, query: str) -> List[ArxivPaper]:
        # Rate limiting
        await self._enforce_rate_limit()

        # Build URL with parameters
        params = {
            "search_query": f"cat:{self.search_category}",
            "max_results": self.max_results,
            "sortBy": "submittedDate"
        }

        # Async HTTP request with timeout
        async with httpx.AsyncClient() as client:
            try:
                response = await client.get(
                    self.base_url,
                    params=params,
                    timeout=self.timeout_seconds
                )
                response.raise_for_status()  # Raise exception for HTTP errors
                return await self._parse_response(response.text)

            except httpx.TimeoutException:
                raise ArxivAPITimeoutError("Request timed out")
            except httpx.HTTPStatusError as e:
                raise ArxivAPIException(f"HTTP {e.response.status_code}")
```

---

## Learning Progress Questions

### Section 1: Basic Syntax and Structure

**Question 1.1:** Convert this Java class to Python:
```java
public class SearchConfig {
    private String query;
    private int maxResults = 10;
    private List<String> categories;

    public SearchConfig(String query) {
        this.query = query;
        this.categories = new ArrayList<>();
    }

    public String getQuery() { return query; }
    public void setQuery(String query) { this.query = query; }
}
```

**Expected Answer:**
```python
from typing import List

class SearchConfig:
    def __init__(self, query: str):
        self.query = query
        self.max_results: int = 10
        self.categories: List[str] = []

    # Python doesn't need explicit getters/setters
    # Direct attribute access: config.query, config.max_results
```

**Question 1.2:** What's the Python equivalent of Java's `Map<String, Object>`?

**Answer:** `Dict[str, Any]` or just `dict` for simple cases.

### Section 2: Data Structures

**Question 2.1:** Given this Python code from the project, explain what happens:
```python
namespaces = {
    "atom": "http://www.w3.org/2005/Atom",
    "arxiv": "http://arxiv.org/schemas/atom"
}
atom_url = namespaces.get("atom", "default_url")
```

**Answer:** Creates a dictionary with string keys and values. The `get()` method safely retrieves the value for "atom" key, returning "default_url" if the key doesn't exist (preventing KeyError).

**Question 2.2:** Convert this Java stream operation to Python:
```java
List<String> upperAuthors = authors.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

**Answer:**
```python
upper_authors = [author.upper() for author in authors]
# Or using map():
upper_authors = list(map(str.upper, authors))
```

### Section 3: OOP and Classes

**Question 3.1:** In the Paper model, what does this line do?
```python
created_at = Column(DateTime, default=lambda: datetime.now(timezone.utc))
```

**Answer:** Creates a database column that automatically sets the current UTC timestamp when a new Paper record is created. The `lambda` creates an anonymous function that's called each time a new record is inserted.

**Question 3.2:** What's the difference between `@property` and `@cached_property`?

**Answer:**
- `@property`: Computed every time it's accessed (like a getter method)
- `@cached_property`: Computed once on first access, then cached for subsequent calls

### Section 4: Functions and Decorators

**Question 4.1:** Explain this decorator pattern from the code:
```python
@lru_cache
def get_settings() -> Settings:
    return Settings()
```

**Answer:** The `@lru_cache` decorator caches the function's return value. Since `get_settings()` takes no parameters, it will only create one Settings instance and return the same instance on subsequent calls (singleton pattern).

**Question 4.2:** What does this function signature mean?
```python
def get_db_session(database: Annotated[BaseDatabase, Depends(get_database)]) -> Generator[Session, None, None]:
```

**Answer:**
- `database` parameter has type `BaseDatabase` and uses FastAPI's dependency injection
- Returns a Generator that yields `Session` objects
- The `Generator[Session, None, None]` means: yields Session, sends None, returns None

### Section 5: Async Programming

**Question 5.1:** What's wrong with this code?
```python
async def fetch_data():
    response = httpx.get("https://api.example.com")  # Missing await
    return response.json()
```

**Answer:** Missing `await` keyword. Should be:
```python
async def fetch_data():
    async with httpx.AsyncClient() as client:
        response = await client.get("https://api.example.com")
        return response.json()
```

**Question 5.2:** Explain the difference between these two approaches:
```python
# Approach 1
@asynccontextmanager
async def lifespan(app: FastAPI):
    # startup
    yield
    # shutdown

# Approach 2
async def startup():
    # startup logic

async def shutdown():
    # shutdown logic
```

**Answer:** Approach 1 uses a context manager that handles both startup and shutdown in one function, ensuring proper cleanup even if errors occur. Approach 2 separates concerns but requires manual error handling.

### Section 6: Type System

**Question 6.1:** What does this Pydantic model validate?
```python
class SearchRequest(BaseModel):
    query: str = Field(..., min_length=1, max_length=500)
    size: int = Field(default=10, ge=1, le=50)
    categories: Optional[List[str]] = Field(default=None)
```

**Answer:**
- `query`: Required string between 1-500 characters
- `size`: Integer between 1-50, defaults to 10
- `categories`: Optional list of strings, defaults to None

**Question 6.2:** What's the difference between `List[str]` and `Optional[List[str]]`?

**Answer:**
- `List[str]`: Must be a list of strings (cannot be None)
- `Optional[List[str]]`: Can be a list of strings OR None (equivalent to `Union[List[str], None]`)

### Section 7: Error Handling

**Question 7.1:** Improve this error handling:
```python
def find_paper(arxiv_id: str):
    paper = database.query(Paper).filter(Paper.arxiv_id == arxiv_id).first()
    return paper
```

**Answer:**
```python
def find_paper(arxiv_id: str) -> Paper:
    paper = database.query(Paper).filter(Paper.arxiv_id == arxiv_id).first()
    if not paper:
        raise PaperNotFound(f"Paper with arXiv ID {arxiv_id} not found")
    return paper
```

**Question 7.2:** What's the Python equivalent of Java's try-with-resources?

**Answer:** The `with` statement:
```python
# Java: try-with-resources
try (Connection conn = getConnection()) {
    // use connection
}

# Python: with statement
with database.get_session() as session:
    # use session
# session automatically closed
```

### Practical Exercises

**Exercise 1:** Create a Python configuration class equivalent to this Java Spring configuration:
```java
@ConfigurationProperties(prefix = "search")
public class SearchProperties {
    private int defaultSize = 10;
    private int maxSize = 100;
    private List<String> allowedCategories = Arrays.asList("cs.AI", "cs.LG");
}
```

**Exercise 2:** Write an async Python function that fetches data from multiple URLs concurrently and returns the combined results.

**Exercise 3:** Create a Pydantic model for a research paper with validation rules:
- Title: required, 1-300 characters
- Authors: required list of 1-20 strings
- Publication date: required date
- Categories: optional list from predefined set

**Exercise 4:** Implement a simple cache decorator that stores function results in a dictionary.

### Assessment Checklist

After completing this study guide, you should be able to:

- [ ] **Read and understand** Python syntax and structure
- [ ] **Convert simple Java classes** to Python equivalents
- [ ] **Use Python data structures** effectively (list, dict, set, tuple)
- [ ] **Write classes with properties** and validation
- [ ] **Understand async/await** concepts and usage
- [ ] **Handle exceptions** properly with try/except
- [ ] **Use type hints** for better code documentation
- [ ] **Import modules** and organize code in packages
- [ ] **Apply decorators** for common patterns (caching, validation)
- [ ] **Work with configuration** and environment variables

### Next Steps

Once you've mastered these fundamentals, you're ready to:

1. **Start Week 1 notebook** with confidence
2. **Explore FastAPI framework** concepts
3. **Understand the project architecture**
4. **Begin hands-on implementation** exercises

The key is to **practice with the actual codebase** - read the files, run the code, modify examples, and see how the concepts work in a real production system.

**Remember:** Python is designed to be readable and concise. When in doubt, choose the simpler, more readable approach - it's usually the "Pythonic" way! 🐍