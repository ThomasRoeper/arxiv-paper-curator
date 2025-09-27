# Phase 1 Coding Exercises: Progressive Testing Challenges

## Why Testing for Learning Python?

**Testing is the PERFECT way to learn Python** because:
- ✅ **Forces deep code understanding** - You must know what code does to test it
- ✅ **Immediate feedback** - Tests pass or fail, no ambiguity
- ✅ **Safe exploration** - Won't break production code
- ✅ **Industry-standard skill** - Every Python developer needs pytest
- ✅ **Reveals edge cases** - Discover how code handles different inputs

## Exercise Structure

Each exercise builds on the previous one, following this progression:

1. **Level 1**: Simple unit tests (pure functions)
2. **Level 2**: Class testing with fixtures
3. **Level 3**: Validation and error testing
4. **Level 4**: Mock-based testing
5. **Level 5**: Async testing patterns
6. **Level 6**: Integration testing

---

## Setup Instructions

### 1. Install Testing Dependencies
```bash
cd /path/to/arxiv-paper-curator
uv sync  # Installs pytest and other dev dependencies
```

### 2. Run Existing Tests (Verify Setup)
```bash
# Run all tests
uv run pytest

# Run specific test file
uv run pytest tests/unit/test_config.py -v

# Run with coverage
uv run pytest --cov=src --cov-report=html
```

### 3. Create Your Exercise Directory
```bash
mkdir tests/exercises
touch tests/exercises/__init__.py
```

---

## Level 1: Simple Unit Tests (Functions & Basic Classes)

### Exercise 1.1: Test Pure Functions

**Goal**: Write tests for utility functions to practice basic pytest patterns.

**Create**: `tests/exercises/test_level1_utils.py`

```python
import pytest
from datetime import datetime, timezone
from typing import List, Dict

# First, implement these utility functions in src/utils/helpers.py
# (You'll create this file as part of the exercise)

def clean_arxiv_id(arxiv_id: str) -> str:
    """Clean and normalize arXiv ID format.

    Examples:
    - "arxiv:2024.01234v1" -> "2024.01234v1"
    - "2024.01234" -> "2024.01234"
    - "http://arxiv.org/abs/2024.01234v1" -> "2024.01234v1"
    """
    # Your implementation here
    pass

def extract_categories(categories: List[str]) -> List[str]:
    """Extract unique categories and sort them.

    Examples:
    - ["cs.AI", "cs.LG", "cs.AI"] -> ["cs.AI", "cs.LG"]
    - ["stat.ML", "cs.LG"] -> ["cs.LG", "stat.ML"]
    """
    # Your implementation here
    pass

def calculate_days_ago(published_date: datetime) -> int:
    """Calculate days between published date and now."""
    # Your implementation here
    pass
```

**Your Task**: Write comprehensive tests:

```python
import pytest
from datetime import datetime, timezone, timedelta
from src.utils.helpers import clean_arxiv_id, extract_categories, calculate_days_ago

class TestArxivIdCleaning:
    """Test arxiv ID cleaning function."""

    def test_clean_arxiv_id_with_prefix(self):
        """Test cleaning arxiv ID with 'arxiv:' prefix."""
        # TODO: Write test
        pass

    def test_clean_arxiv_id_with_url(self):
        """Test cleaning arxiv ID from full URL."""
        # TODO: Write test
        pass

    def test_clean_arxiv_id_already_clean(self):
        """Test cleaning already clean arxiv ID."""
        # TODO: Write test
        pass

    def test_clean_arxiv_id_empty_string(self):
        """Test cleaning empty string raises appropriate error."""
        # TODO: Write test for edge case
        pass

class TestCategoryExtraction:
    """Test category extraction and sorting."""

    def test_extract_unique_categories(self):
        """Test removing duplicates from categories."""
        # TODO: Write test
        pass

    def test_extract_categories_sorted(self):
        """Test categories are returned sorted."""
        # TODO: Write test
        pass

    def test_extract_empty_categories(self):
        """Test handling empty category list."""
        # TODO: Write test
        pass

class TestDateCalculations:
    """Test date calculation utilities."""

    def test_calculate_days_ago_recent(self):
        """Test calculating days for recent date."""
        yesterday = datetime.now(timezone.utc) - timedelta(days=1)
        result = calculate_days_ago(yesterday)
        assert result == 1

    def test_calculate_days_ago_future(self):
        """Test calculating days for future date."""
        # TODO: Write test - should handle future dates gracefully
        pass
```

**Learning Goals**:
- Basic pytest structure
- Test classes and methods
- Assertion patterns
- Edge case testing

---

## Level 2: Configuration Testing with Fixtures

### Exercise 2.1: Advanced Configuration Testing

**Goal**: Test the configuration system using pytest fixtures.

**Create**: `tests/exercises/test_level2_config.py`

```python
import pytest
import os
from unittest.mock import patch
from src.config import Settings, ArxivSettings, OpenSearchSettings

class TestConfigurationSystem:
    """Test configuration with environment variables."""

    @pytest.fixture
    def clean_environment(self):
        """Fixture to provide clean environment for testing."""
        # Save original environment
        original_env = dict(os.environ)

        # Clear relevant environment variables
        test_vars = ['APP_VERSION', 'DEBUG', 'ARXIV__BASE_URL', 'OPENSEARCH__HOST']
        for var in test_vars:
            os.environ.pop(var, None)

        yield  # Test runs here

        # Restore original environment
        os.environ.clear()
        os.environ.update(original_env)

    @pytest.fixture
    def mock_env_vars(self):
        """Fixture providing test environment variables."""
        test_env = {
            'APP_VERSION': '2.0.0',
            'DEBUG': 'false',
            'ARXIV__BASE_URL': 'https://test.arxiv.org/api/query',
            'ARXIV__MAX_RESULTS': '25',
            'OPENSEARCH__HOST': 'https://test-opensearch:9200'
        }

        with patch.dict(os.environ, test_env, clear=False):
            yield test_env

    def test_default_settings(self, clean_environment):
        """Test settings load default values when no env vars set."""
        # TODO: Create Settings instance and test defaults
        pass

    def test_environment_override(self, mock_env_vars):
        """Test settings load from environment variables."""
        # TODO: Test that env vars override defaults
        pass

    def test_nested_settings(self, mock_env_vars):
        """Test nested settings load correctly."""
        settings = Settings()

        # TODO: Test that ARXIV__* variables set arxiv settings
        # TODO: Test that OPENSEARCH__* variables set opensearch settings
        pass

    @pytest.mark.parametrize("invalid_url,expected_error", [
        ("http://invalid-db", "Database URL must start with 'postgresql://'"),
        ("mysql://localhost/db", "Database URL must start with 'postgresql://'"),
        ("", "Database URL must start with 'postgresql://'"),
    ])
    def test_database_url_validation(self, invalid_url, expected_error):
        """Test database URL validation with parametrized tests."""
        # TODO: Test that invalid URLs raise ValidationError with expected message
        pass
```

**Learning Goals**:
- Pytest fixtures for setup/teardown
- Environment variable mocking
- Parametrized tests
- Exception testing with pytest.raises

---

## Level 3: Pydantic Validation Testing

### Exercise 3.1: Schema Validation Mastery

**Goal**: Master Pydantic validation testing patterns.

**Create**: `tests/exercises/test_level3_validation.py`

```python
import pytest
from pydantic import ValidationError
from datetime import datetime
from src.schemas.api.search import SearchRequest, HybridSearchRequest

class TestSearchRequestValidation:
    """Comprehensive validation testing for SearchRequest."""

    def test_valid_search_request_minimal(self):
        """Test valid SearchRequest with minimal required fields."""
        # TODO: Create SearchRequest with only required fields
        # TODO: Assert all fields have expected values
        pass

    def test_valid_search_request_all_fields(self):
        """Test valid SearchRequest with all fields specified."""
        # TODO: Create SearchRequest with all optional fields
        # TODO: Assert all fields are set correctly
        pass

    @pytest.mark.parametrize("invalid_query,expected_error", [
        ("", "ensure this value has at least 1 character"),
        ("a" * 501, "ensure this value has at most 500 characters"),
        (None, "none is not an allowed value"),
        (123, "str type expected"),
    ])
    def test_query_validation_errors(self, invalid_query, expected_error):
        """Test query field validation with various invalid inputs."""
        # TODO: Test that invalid queries raise ValidationError
        # TODO: Check that error message contains expected text
        pass

    @pytest.mark.parametrize("invalid_size,expected_behavior", [
        (0, "should raise ValidationError"),
        (51, "should raise ValidationError"),
        (-1, "should raise ValidationError"),
        ("10", "should convert to int 10"),
        (10.5, "should convert to int 10"),
    ])
    def test_size_validation_and_coercion(self, invalid_size, expected_behavior):
        """Test size field validation and type coercion."""
        # TODO: Implement test logic based on expected_behavior
        # TODO: Test both validation errors and type coercion
        pass

    def test_categories_list_validation(self):
        """Test categories field accepts various list formats."""
        # TODO: Test empty list
        # TODO: Test list with valid categories
        # TODO: Test None (should be accepted)
        # TODO: Test invalid types (should raise ValidationError)
        pass

class TestHybridSearchRequestExtended:
    """Test extended HybridSearchRequest validation."""

    def test_hybrid_request_inherits_base_validation(self):
        """Test that HybridSearchRequest inherits base SearchRequest validation."""
        # TODO: Test that base validations still work
        pass

    def test_use_hybrid_boolean_coercion(self):
        """Test use_hybrid field boolean coercion."""
        test_cases = [
            (True, True),
            (False, False),
            ("true", True),
            ("false", False),
            (1, True),
            (0, False),
            ("yes", True),  # Check if this works
            ("no", False),  # Check if this works
        ]

        for input_value, expected in test_cases:
            # TODO: Test boolean coercion for each case
            pass

    def test_min_score_validation(self):
        """Test min_score field validation."""
        # TODO: Test valid scores (>= 0.0)
        # TODO: Test invalid scores (< 0.0)
        # TODO: Test type coercion (string to float)
        pass
```

**Learning Goals**:
- Advanced parametrized testing
- Pydantic ValidationError handling
- Type coercion testing
- Inheritance testing in Pydantic models

---

## Level 4: Mock-Based Testing

### Exercise 4.1: HTTP Client Testing with Mocks

**Goal**: Learn to test code that makes external API calls using mocks.

**Create**: `tests/exercises/test_level4_mocking.py`

```python
import pytest
from unittest.mock import AsyncMock, MagicMock, patch, mock_open
import httpx
from src.services.arxiv.client import ArxivClient
from src.config import ArxivSettings
from src.exceptions import ArxivAPITimeoutError, ArxivAPIException

class TestArxivClientMocking:
    """Test ArxivClient with comprehensive mocking."""

    @pytest.fixture
    def arxiv_client(self):
        """Create test ArxivClient with minimal settings."""
        settings = ArxivSettings(
            base_url="https://test.arxiv.org/api/query",
            rate_limit_delay=0.1,  # Fast for tests
            timeout_seconds=5,
            max_results=10,
            pdf_cache_dir="/tmp/test_cache"
        )
        return ArxivClient(settings)

    @pytest.fixture
    def mock_successful_response(self):
        """Mock successful arXiv API response."""
        return """<?xml version="1.0" encoding="UTF-8"?>
        <feed xmlns="http://www.w3.org/2005/Atom">
          <entry>
            <id>http://arxiv.org/abs/2024.0001v1</id>
            <title>Test AI Paper</title>
            <summary>This is a test paper about artificial intelligence.</summary>
            <author><name>John Doe</name></author>
            <published>2024-01-01T00:00:00Z</published>
            <arxiv:primary_category xmlns:arxiv="http://arxiv.org/schemas/atom" term="cs.AI"/>
            <link href="http://arxiv.org/pdf/2024.0001v1" rel="alternate" type="application/pdf"/>
          </entry>
        </feed>"""

    @pytest.mark.asyncio
    async def test_fetch_papers_success(self, arxiv_client, mock_successful_response):
        """Test successful paper fetching with mocked HTTP response."""
        # TODO: Mock httpx.AsyncClient.get to return successful response
        # TODO: Call arxiv_client.fetch_papers()
        # TODO: Assert returned papers have expected structure
        # TODO: Assert HTTP client was called with correct parameters
        pass

    @pytest.mark.asyncio
    async def test_fetch_papers_timeout(self, arxiv_client):
        """Test timeout handling in fetch_papers."""
        # TODO: Mock httpx.AsyncClient.get to raise TimeoutException
        # TODO: Assert that ArxivAPITimeoutError is raised
        # TODO: Assert error message contains useful information
        pass

    @pytest.mark.asyncio
    async def test_fetch_papers_http_error(self, arxiv_client):
        """Test HTTP error handling."""
        # TODO: Mock httpx.AsyncClient.get to raise HTTPStatusError
        # TODO: Assert that ArxivAPIException is raised
        # TODO: Test different HTTP status codes (404, 500, etc.)
        pass

    @pytest.mark.asyncio
    async def test_rate_limiting_enforcement(self, arxiv_client):
        """Test that rate limiting delays are enforced."""
        # TODO: Mock time.time() to control timestamps
        # TODO: Mock asyncio.sleep to verify it's called
        # TODO: Call fetch_papers multiple times rapidly
        # TODO: Assert sleep was called with correct delay
        pass

    @patch('src.services.arxiv.client.Path.mkdir')
    @patch('builtins.open', new_callable=mock_open)
    def test_pdf_download_success(self, mock_file, mock_mkdir, arxiv_client):
        """Test successful PDF download with file mocking."""
        # TODO: Mock httpx response for PDF download
        # TODO: Test that file is written correctly
        # TODO: Assert mkdir was called for cache directory
        # TODO: Assert file content matches mock response
        pass

class TestCacheClientMocking:
    """Test Redis cache client with mocking."""

    @pytest.fixture
    def mock_redis_client(self):
        """Mock Redis client."""
        return MagicMock()

    @pytest.fixture
    def cache_client(self, mock_redis_client):
        """Create CacheClient with mocked Redis."""
        from src.services.cache.client import CacheClient
        from src.config import RedisSettings

        settings = RedisSettings(ttl_hours=1)
        return CacheClient(mock_redis_client, settings)

    def test_cache_key_generation(self, cache_client):
        """Test cache key generation for different requests."""
        from src.schemas.api.ask import AskRequest

        # TODO: Create multiple AskRequest instances
        # TODO: Test that identical requests generate identical keys
        # TODO: Test that different requests generate different keys
        # TODO: Test key format and length
        pass

    @pytest.mark.asyncio
    async def test_cache_hit(self, cache_client, mock_redis_client):
        """Test cache hit scenario."""
        # TODO: Mock redis.get to return cached response
        # TODO: Call find_cached_response
        # TODO: Assert cached response is returned
        # TODO: Assert redis.get was called with correct key
        pass

    @pytest.mark.asyncio
    async def test_cache_miss(self, cache_client, mock_redis_client):
        """Test cache miss scenario."""
        # TODO: Mock redis.get to return None
        # TODO: Call find_cached_response
        # TODO: Assert None is returned
        pass
```

**Learning Goals**:
- unittest.mock module usage
- Mocking external dependencies (HTTP, file system, Redis)
- Async testing with mocks
- Patch decorators and context managers
- Testing error conditions

---

## Level 5: Async Testing Patterns

### Exercise 5.1: Advanced Async Testing

**Goal**: Master async testing patterns and concurrent operations.

**Create**: `tests/exercises/test_level5_async.py`

```python
import pytest
import asyncio
from unittest.mock import AsyncMock, MagicMock, patch
from src.services.ollama.client import OllamaClient
from src.config import Settings

class TestAsyncPatterns:
    """Test various async patterns used in the codebase."""

    @pytest.fixture
    def ollama_client(self):
        """Create OllamaClient for testing."""
        settings = Settings(
            ollama_host="http://test-ollama:11434",
            ollama_model="test-model",
            ollama_timeout=30
        )
        return OllamaClient(settings)

    @pytest.mark.asyncio
    async def test_health_check_success(self, ollama_client):
        """Test successful health check."""
        # TODO: Mock httpx.AsyncClient.get for health endpoint
        # TODO: Mock successful response
        # TODO: Call health_check and assert result
        pass

    @pytest.mark.asyncio
    async def test_concurrent_requests(self, ollama_client):
        """Test handling multiple concurrent requests."""
        # TODO: Mock multiple async responses
        # TODO: Create multiple concurrent tasks
        # TODO: Use asyncio.gather to run concurrently
        # TODO: Assert all requests complete successfully
        pass

    @pytest.mark.asyncio
    async def test_timeout_handling(self, ollama_client):
        """Test timeout handling in async operations."""
        # TODO: Mock slow response (longer than timeout)
        # TODO: Assert timeout exception is raised
        # TODO: Test that partial results are handled correctly
        pass

    @pytest.mark.asyncio
    async def test_async_context_manager(self):
        """Test async context manager patterns."""
        # TODO: Create a mock async context manager
        # TODO: Test proper setup and teardown
        # TODO: Test exception handling in context manager
        pass

class TestAsyncGenerators:
    """Test async generator patterns for streaming."""

    @pytest.mark.asyncio
    async def test_streaming_response_mock(self):
        """Test streaming response generation."""

        async def mock_stream_generator():
            """Mock streaming generator."""
            responses = [
                "Hello",
                " world",
                "!",
                " This",
                " is",
                " streaming."
            ]
            for response in responses:
                yield response
                await asyncio.sleep(0.01)  # Simulate delay

        # TODO: Collect all streamed responses
        # TODO: Assert complete message is "Hello world! This is streaming."
        # TODO: Assert responses arrive in correct order
        pass

    @pytest.mark.asyncio
    async def test_error_in_stream(self):
        """Test error handling in async generators."""

        async def failing_generator():
            yield "First chunk"
            yield "Second chunk"
            raise Exception("Stream error")
            yield "This should not be reached"

        # TODO: Collect responses until error
        # TODO: Assert first two chunks are received
        # TODO: Assert exception is properly raised
        pass

class TestAsyncDatabase:
    """Test async database operations."""

    @pytest.fixture
    def mock_session(self):
        """Mock database session."""
        session = AsyncMock()
        # TODO: Configure mock session for common operations
        return session

    @pytest.mark.asyncio
    async def test_async_database_query(self, mock_session):
        """Test async database query patterns."""
        # TODO: Mock query result
        # TODO: Test async query execution
        # TODO: Assert proper session handling
        pass

    @pytest.mark.asyncio
    async def test_database_transaction_rollback(self, mock_session):
        """Test transaction rollback on error."""
        # TODO: Mock session to raise exception during commit
        # TODO: Test that rollback is called
        # TODO: Assert proper error handling
        pass
```

**Learning Goals**:
- @pytest.mark.asyncio decorator
- AsyncMock usage patterns
- Testing concurrent operations
- Async context managers
- Streaming/generator testing
- Error handling in async code

---

## Level 6: Integration Testing

### Exercise 6.1: Full Stack Integration Tests

**Goal**: Test complete workflows that integrate multiple components.

**Create**: `tests/exercises/test_level6_integration.py`

```python
import pytest
from unittest.mock import patch, MagicMock
from fastapi.testclient import TestClient
from src.main import app

class TestAPIIntegration:
    """Integration tests for API endpoints."""

    @pytest.fixture
    def test_client(self):
        """Create test client for API testing."""
        return TestClient(app)

    @pytest.fixture
    def mock_dependencies(self):
        """Mock all external dependencies."""
        with patch('src.dependencies.get_opensearch_client') as mock_opensearch, \
             patch('src.dependencies.get_ollama_client') as mock_ollama, \
             patch('src.dependencies.get_cache_client') as mock_cache:

            # Configure mocks
            mock_opensearch.return_value = MagicMock()
            mock_ollama.return_value = MagicMock()
            mock_cache.return_value = MagicMock()

            yield {
                'opensearch': mock_opensearch.return_value,
                'ollama': mock_ollama.return_value,
                'cache': mock_cache.return_value
            }

    def test_health_endpoint_integration(self, test_client, mock_dependencies):
        """Test health endpoint with all dependencies mocked."""
        # TODO: Configure mock dependencies to return healthy status
        # TODO: Make GET request to /api/v1/health
        # TODO: Assert response status code and structure
        # TODO: Assert all services report as healthy
        pass

    def test_search_endpoint_integration(self, test_client, mock_dependencies):
        """Test search endpoint integration."""
        # TODO: Configure opensearch mock to return search results
        # TODO: Make POST request to search endpoint
        # TODO: Assert response structure matches SearchResponse schema
        # TODO: Assert opensearch client was called with correct parameters
        pass

    def test_ask_endpoint_integration(self, test_client, mock_dependencies):
        """Test RAG ask endpoint integration."""
        # TODO: Configure cache mock (cache miss)
        # TODO: Configure opensearch mock (search results)
        # TODO: Configure ollama mock (LLM response)
        # TODO: Make POST request to ask endpoint
        # TODO: Assert complete workflow execution
        # TODO: Assert response saved to cache
        pass

class TestDataPipelineIntegration:
    """Integration tests for data processing pipeline."""

    @pytest.fixture
    def pipeline_mocks(self):
        """Mock all pipeline components."""
        with patch('src.services.arxiv.client.httpx.AsyncClient') as mock_http, \
             patch('src.services.pdf_parser.parser.PDFParserService') as mock_parser, \
             patch('src.repositories.paper.PaperRepository') as mock_repo:

            yield {
                'http': mock_http,
                'parser': mock_parser,
                'repository': mock_repo
            }

    @pytest.mark.asyncio
    async def test_paper_ingestion_workflow(self, pipeline_mocks):
        """Test complete paper ingestion workflow."""
        # TODO: Configure arxiv API mock response
        # TODO: Configure PDF parser mock response
        # TODO: Configure repository mock
        # TODO: Execute ingestion workflow
        # TODO: Assert paper data flows through all components
        # TODO: Assert final data is saved to repository
        pass

    @pytest.mark.asyncio
    async def test_error_handling_in_pipeline(self, pipeline_mocks):
        """Test error handling across pipeline components."""
        # TODO: Configure one component to fail
        # TODO: Execute workflow
        # TODO: Assert error is handled gracefully
        # TODO: Assert partial data is preserved
        pass

class TestSearchWorkflow:
    """Integration tests for search workflows."""

    @pytest.mark.asyncio
    async def test_hybrid_search_workflow(self):
        """Test complete hybrid search workflow."""
        # TODO: Mock embeddings service
        # TODO: Mock opensearch with both BM25 and vector results
        # TODO: Execute hybrid search
        # TODO: Assert results are properly fused
        # TODO: Assert relevance scoring works
        pass

    @pytest.mark.asyncio
    async def test_search_with_caching(self):
        """Test search workflow with caching."""
        # TODO: Mock cache miss on first search
        # TODO: Mock cache hit on second identical search
        # TODO: Execute same search twice
        # TODO: Assert first search hits database
        # TODO: Assert second search hits cache
        pass
```

**Learning Goals**:
- FastAPI TestClient usage
- Integration test patterns
- Complex mocking scenarios
- Workflow testing
- Error propagation testing

---

## Progress Tracking and Assessment

### Exercise Completion Checklist

Track your progress through each level:

#### Level 1: Simple Unit Tests
- [ ] Exercise 1.1: Created utility functions and comprehensive tests
- [ ] Can write basic test classes and methods
- [ ] Understand pytest assertion patterns
- [ ] Can test edge cases and error conditions

#### Level 2: Configuration Testing
- [ ] Exercise 2.1: Mastered pytest fixtures
- [ ] Can mock environment variables
- [ ] Understand parametrized testing
- [ ] Can test configuration validation

#### Level 3: Validation Testing
- [ ] Exercise 3.1: Mastered Pydantic validation testing
- [ ] Understand complex parametrized tests
- [ ] Can test type coercion and validation errors
- [ ] Know how to test schema inheritance

#### Level 4: Mock-Based Testing
- [ ] Exercise 4.1: Can mock external dependencies
- [ ] Understand AsyncMock for async code
- [ ] Can mock file operations and HTTP calls
- [ ] Know how to test error conditions with mocks

#### Level 5: Async Testing
- [ ] Exercise 5.1: Mastered async testing patterns
- [ ] Can test concurrent operations
- [ ] Understand async generators and streaming
- [ ] Can test async context managers

#### Level 6: Integration Testing
- [ ] Exercise 6.1: Can write integration tests
- [ ] Understand FastAPI TestClient
- [ ] Can test complete workflows
- [ ] Know how to mock complex dependency chains

### Self-Assessment Questions

After completing each level, answer these questions:

1. **Can you explain what the code does** by reading the test?
2. **Can you predict what will break** if you change the implementation?
3. **Do you understand the Python patterns** used in the tests?
4. **Can you write similar tests** for other parts of the codebase?

### Advanced Challenges (Optional)

Once you complete the basic exercises:

#### Challenge 1: Test Coverage Analysis
```bash
# Run tests with coverage
uv run pytest --cov=src --cov-report=html tests/exercises/

# Open coverage report
open htmlcov/index.html

# Goal: Achieve >90% coverage on code you tested
```

#### Challenge 2: Property-Based Testing
```python
# Install hypothesis for property-based testing
pip install hypothesis

# Write property-based tests for utility functions
from hypothesis import given, strategies as st

@given(st.text(min_size=1, max_size=100))
def test_clean_arxiv_id_properties(arxiv_id):
    """Property-based test for arxiv ID cleaning."""
    result = clean_arxiv_id(f"arxiv:{arxiv_id}")
    assert not result.startswith("arxiv:")
    assert len(result) > 0
```

#### Challenge 3: Performance Testing
```python
import time
import pytest

def test_search_performance():
    """Test that search completes within reasonable time."""
    start_time = time.time()

    # Execute search operation
    result = perform_search("test query")

    end_time = time.time()
    duration = end_time - start_time

    assert duration < 1.0  # Should complete within 1 second
    assert len(result) > 0
```

#### Challenge 4: Mutation Testing
```bash
# Install mutmut for mutation testing
pip install mutmut

# Run mutation tests on your code
mutmut run --paths-to-mutate=src/utils/helpers.py

# Goal: Achieve high mutation test score (>80%)
```

## Why This Approach Works

### For Java Developers:
- **Familiar patterns**: Test classes, setup/teardown, assertions
- **Clear progression**: From simple to complex, building on previous knowledge
- **Immediate feedback**: Tests pass/fail - no ambiguity
- **Production relevance**: Testing is critical in enterprise development

### For Python Learning:
- **Forces code reading**: Must understand code to test it
- **Practical application**: Learn by doing real tasks
- **Error discovery**: Find edge cases and error conditions
- **Best practices**: Learn pytest idioms and patterns

### For AI Project Understanding:
- **System comprehension**: Understand how components interact
- **API patterns**: Learn FastAPI testing approaches
- **Async patterns**: Master modern Python async/await
- **Integration knowledge**: See how pieces fit together

## Getting Help

### When You're Stuck:
1. **Read existing tests** in the project for patterns
2. **Check pytest documentation**: https://docs.pytest.org/
3. **Look at error messages** - they're usually helpful
4. **Use debugger**: `pytest --pdb` drops into debugger on failure
5. **Add print statements** to understand test flow

### Useful Commands:
```bash
# Run specific test
uv run pytest tests/exercises/test_level1_utils.py::TestArxivIdCleaning::test_clean_arxiv_id_with_prefix -v

# Run with output
uv run pytest tests/exercises/ -s

# Run with debugger
uv run pytest tests/exercises/ --pdb

# Run only failed tests
uv run pytest tests/exercises/ --lf
```

## Next Steps

After completing these exercises, you'll have:
- **Solid Python testing skills**
- **Deep understanding of the codebase**
- **Confidence to modify and extend the system**
- **Ready for Week 1 implementation tasks**

**Most importantly**: You'll have learned Python by solving real problems with immediate feedback - the most effective way to master a new language! 🚀

Start with Level 1 and work your way up. Each exercise builds on the previous one, creating a solid foundation for your Python and AI development journey.