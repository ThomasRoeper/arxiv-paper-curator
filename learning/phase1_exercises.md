# Phase 1 Exercises: Python Fundamentals for Java Developers

## Exercise 1: Basic Python Syntax

### Task 1.1: Create a Simple Class
Create a file `learning/exercises/student.py` with a Student class:

```python
class Student:
    """Represents a student with name, grades, and GPA calculation."""

    def __init__(self, name: str, student_id: int):
        self.name = name
        self.student_id = student_id
        self.grades: list[float] = []

    def add_grade(self, grade: float) -> None:
        """Add a grade to the student's record."""
        if 0 <= grade <= 100:
            self.grades.append(grade)
        else:
            raise ValueError(f"Grade must be between 0 and 100, got {grade}")

    def calculate_gpa(self) -> float:
        """Calculate GPA from grades (0-4 scale)."""
        if not self.grades:
            return 0.0
        return sum(self.grades) / len(self.grades) / 25.0  # Simple conversion

    def __str__(self) -> str:
        """String representation of student."""
        return f"Student({self.name}, ID: {self.student_id}, GPA: {self.calculate_gpa():.2f})"

    def __repr__(self) -> str:
        """Developer representation."""
        return f"Student(name={self.name!r}, student_id={self.student_id})"
```

**Java Comparison:**
```java
// Java equivalent (much more verbose)
public class Student {
    private String name;
    private int studentId;
    private List<Double> grades;

    public Student(String name, int studentId) {
        this.name = name;
        this.studentId = studentId;
        this.grades = new ArrayList<>();
    }

    public void addGrade(double grade) {
        if (grade < 0 || grade > 100) {
            throw new IllegalArgumentException("Grade must be between 0 and 100");
        }
        grades.add(grade);
    }

    public double calculateGpa() {
        if (grades.isEmpty()) return 0.0;
        return grades.stream()
            .mapToDouble(Double::doubleValue)
            .average()
            .orElse(0.0) / 25.0;
    }

    @Override
    public String toString() {
        return String.format("Student(%s, ID: %d, GPA: %.2f)",
            name, studentId, calculateGpa());
    }
}
```

**Test it:**
```python
# Create student
student = Student("Alice", 12345)

# Add grades
student.add_grade(85.5)
student.add_grade(92.0)
student.add_grade(88.5)

# Check GPA
print(student)  # Student(Alice, ID: 12345, GPA: 3.54)
print(repr(student))  # Student(name='Alice', student_id=12345)
```

### Task 1.2: Python Collections
Compare Python collections with Java:

```python
# Lists (like ArrayList)
numbers = [1, 2, 3, 4, 5]
numbers.append(6)  # Java: list.add(6)
numbers[0] = 10     # Java: list.set(0, 10)
length = len(numbers)  # Java: list.size()

# List comprehension (like Stream API but cleaner)
squares = [x**2 for x in numbers]
# Java: list.stream().map(x -> x*x).collect(Collectors.toList())

evens = [x for x in numbers if x % 2 == 0]
# Java: list.stream().filter(x -> x % 2 == 0).collect(Collectors.toList())

# Dictionaries (like HashMap)
student_ages = {"Alice": 20, "Bob": 21, "Charlie": 19}
student_ages["Diana"] = 22  # Java: map.put("Diana", 22)
age = student_ages.get("Alice")  # Java: map.get("Alice")
age = student_ages.get("Unknown", 0)  # Java: map.getOrDefault("Unknown", 0)

# Dictionary comprehension
age_map = {name: age + 1 for name, age in student_ages.items()}

# Sets (like HashSet)
unique_numbers = {1, 2, 3, 3, 4}  # {1, 2, 3, 4}
unique_numbers.add(5)  # Java: set.add(5)

# Tuples (immutable lists, no Java equivalent)
coordinates = (10, 20)  # Cannot be modified
x, y = coordinates  # Unpacking
```

**Exercise:**
Write functions using list comprehensions:

```python
def filter_passing_students(students: list[Student], min_gpa: float) -> list[Student]:
    """Return students with GPA >= min_gpa."""
    return [s for s in students if s.calculate_gpa() >= min_gpa]

def get_student_names(students: list[Student]) -> list[str]:
    """Extract names from student list."""
    return [s.name for s in students]

def create_student_map(students: list[Student]) -> dict[int, Student]:
    """Map student_id -> Student."""
    return {s.student_id: s for s in students}
```

---

## Exercise 2: Type Hints and Pydantic

### Task 2.1: Study Pydantic Models
Read `src/schemas/api/search.py` and create your own model:

```python
from pydantic import BaseModel, Field, field_validator
from datetime import datetime

class CourseEnrollment(BaseModel):
    """Course enrollment request."""
    student_id: int = Field(..., gt=0, description="Student ID must be positive")
    course_code: str = Field(..., min_length=3, max_length=10)
    semester: str = Field(..., pattern=r"^(Fall|Spring|Summer) \d{4}$")
    enrolled_at: datetime = Field(default_factory=datetime.now)

    @field_validator("course_code")
    @classmethod
    def validate_course_code(cls, v: str) -> str:
        """Ensure course code is uppercase."""
        return v.upper()

    model_config = {
        "json_schema_extra": {
            "examples": [
                {
                    "student_id": 12345,
                    "course_code": "CS101",
                    "semester": "Fall 2024"
                }
            ]
        }
    }
```

**Test validation:**
```python
# Valid
enrollment = CourseEnrollment(
    student_id=12345,
    course_code="cs101",  # Will be uppercased
    semester="Fall 2024"
)
print(enrollment.course_code)  # CS101

# Invalid - raises ValidationError
try:
    bad_enrollment = CourseEnrollment(
        student_id=-1,  # Must be positive
        course_code="AB",  # Too short
        semester="Invalid"  # Wrong format
    )
except Exception as e:
    print(e)
```

**Java Comparison:**
```java
// Java Bean Validation (less powerful than Pydantic)
public class CourseEnrollment {
    @Positive(message = "Student ID must be positive")
    private int studentId;

    @Size(min = 3, max = 10)
    private String courseCode;

    @Pattern(regexp = "^(Fall|Spring|Summer) \\d{4}$")
    private String semester;

    private LocalDateTime enrolledAt = LocalDateTime.now();

    // Getters, setters, constructors...
}
```

Pydantic advantages:
- Automatic type coercion
- Custom validators as class methods
- JSON serialization built-in
- Better error messages

### Task 2.2: Compare with Project Models
Study these files and note differences:

1. **Database Model (ORM):**
   ```python
   # src/models/paper.py
   # Uses SQLAlchemy (like JPA)
   class Paper(Base):
       __tablename__ = "papers"
       id = Column(Integer, primary_key=True)
       title = Column(String, nullable=False)
   ```

2. **API Model (Validation):**
   ```python
   # src/schemas/api/search.py
   # Uses Pydantic (like Bean Validation + DTO)
   class SearchRequest(BaseModel):
       query: str
       mode: SearchMode = SearchMode.HYBRID
   ```

**Exercise:**
Create both ORM and Pydantic models for a `Course` entity:
- Database model: `learning/exercises/course_model.py`
- API model: `learning/exercises/course_schema.py`

---

## Exercise 3: Async Programming

### Task 3.1: Basic Async Operations
Create `learning/exercises/async_basics.py`:

```python
import asyncio
import httpx
from time import time

# Synchronous version (slow)
def fetch_url_sync(url: str) -> str:
    """Fetch URL synchronously - blocks until complete."""
    response = httpx.get(url)
    return response.text

# Async version (fast)
async def fetch_url_async(url: str) -> str:
    """Fetch URL asynchronously - doesn't block."""
    async with httpx.AsyncClient() as client:
        response = await client.get(url)
        return response.text

# Fetch multiple URLs sequentially (slow)
async def fetch_sequential(urls: list[str]) -> list[str]:
    results = []
    for url in urls:
        result = await fetch_url_async(url)
        results.append(result)
    return results

# Fetch multiple URLs concurrently (fast!)
async def fetch_concurrent(urls: list[str]) -> list[str]:
    """Run all requests concurrently using asyncio.gather."""
    tasks = [fetch_url_async(url) for url in urls]
    return await asyncio.gather(*tasks)

# Test performance
async def test_performance():
    urls = [
        "https://httpbin.org/delay/1",
        "https://httpbin.org/delay/1",
        "https://httpbin.org/delay/1",
    ]

    # Sequential: ~3 seconds (1+1+1)
    start = time()
    await fetch_sequential(urls)
    print(f"Sequential: {time() - start:.2f}s")

    # Concurrent: ~1 second (max of 1,1,1)
    start = time()
    await fetch_concurrent(urls)
    print(f"Concurrent: {time() - start:.2f}s")

# Run async function
if __name__ == "__main__":
    asyncio.run(test_performance())
```

**Java Comparison:**
```java
// Java CompletableFuture (more verbose)
CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> fetchUrl(url1));
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> fetchUrl(url2));
CompletableFuture<String> future3 = CompletableFuture.supplyAsync(() -> fetchUrl(url3));

CompletableFuture<Void> allOf = CompletableFuture.allOf(future1, future2, future3);
allOf.join();

List<String> results = List.of(
    future1.join(),
    future2.join(),
    future3.join()
);
```

Python's async/await is cleaner and more readable.

### Task 3.2: Study Project's Async Code
Read `src/routers/hybrid_search.py` and understand:

```python
@router.get("/search", response_model=SearchResponse)
async def search(
    q: str = Query(..., description="Search query"),
    mode: SearchMode = Query(SearchMode.HYBRID),
    opensearch: OpenSearchClient = Depends(get_opensearch_client)
):
    """Search endpoint - async allows handling multiple requests efficiently."""
    results = await opensearch.search(q, mode)  # Await async operation
    return SearchResponse(results=results)
```

**Key points:**
- `async def` declares async function
- `await` waits for async operation (doesn't block other requests)
- FastAPI handles async automatically
- Multiple requests run concurrently

**Exercise:**
Create an async endpoint that fetches data from multiple sources:

```python
async def get_paper_with_citations(paper_id: str):
    """Fetch paper and its citations concurrently."""
    paper_task = fetch_paper(paper_id)
    citations_task = fetch_citations(paper_id)

    # Run concurrently
    paper, citations = await asyncio.gather(paper_task, citations_task)

    return {
        "paper": paper,
        "citations": citations
    }
```

---

## Exercise 4: Testing with Pytest

### Task 4.1: Unit Tests
Create `learning/exercises/test_student.py`:

```python
import pytest
from learning.exercises.student import Student

def test_student_creation():
    """Test creating a student."""
    student = Student("Alice", 12345)
    assert student.name == "Alice"
    assert student.student_id == 12345
    assert student.grades == []

def test_add_grade():
    """Test adding grades."""
    student = Student("Bob", 67890)
    student.add_grade(85.5)
    student.add_grade(92.0)
    assert len(student.grades) == 2
    assert 85.5 in student.grades

def test_invalid_grade():
    """Test that invalid grades raise ValueError."""
    student = Student("Charlie", 11111)
    with pytest.raises(ValueError, match="Grade must be between 0 and 100"):
        student.add_grade(150)

def test_gpa_calculation():
    """Test GPA calculation."""
    student = Student("Diana", 22222)
    student.add_grade(75)   # 3.0
    student.add_grade(100)  # 4.0
    assert student.calculate_gpa() == pytest.approx(3.5)

def test_empty_gpa():
    """Test GPA with no grades."""
    student = Student("Eve", 33333)
    assert student.calculate_gpa() == 0.0

# Parametrized test (run same test with different inputs)
@pytest.mark.parametrize("grades,expected_gpa", [
    ([100, 100, 100], 4.0),
    ([75, 75, 75], 3.0),
    ([50, 50, 50], 2.0),
    ([25, 25, 25], 1.0),
])
def test_gpa_scenarios(grades, expected_gpa):
    """Test various GPA scenarios."""
    student = Student("Test", 99999)
    for grade in grades:
        student.add_grade(grade)
    assert student.calculate_gpa() == pytest.approx(expected_gpa)
```

**Run tests:**
```bash
uv run pytest learning/exercises/test_student.py -v
```

### Task 4.2: Async Tests
Create `learning/exercises/test_async.py`:

```python
import pytest
from learning.exercises.async_basics import fetch_url_async, fetch_concurrent

@pytest.mark.asyncio
async def test_fetch_url():
    """Test async URL fetching."""
    result = await fetch_url_async("https://httpbin.org/get")
    assert "headers" in result

@pytest.mark.asyncio
async def test_concurrent_fetch():
    """Test concurrent fetching."""
    urls = ["https://httpbin.org/get"] * 3
    results = await fetch_concurrent(urls)
    assert len(results) == 3
```

**Java Comparison:**
```java
// JUnit 5
@Test
void testStudentCreation() {
    Student student = new Student("Alice", 12345);
    assertEquals("Alice", student.getName());
    assertEquals(12345, student.getStudentId());
}

@ParameterizedTest
@CsvSource({
    "100,100,100, 4.0",
    "75,75,75, 3.0"
})
void testGpaScenarios(double grade1, double grade2, double grade3, double expected) {
    Student student = new Student("Test", 99999);
    student.addGrade(grade1);
    student.addGrade(grade2);
    student.addGrade(grade3);
    assertEquals(expected, student.calculateGpa(), 0.01);
}
```

Pytest advantages:
- No class required (functions are fine)
- Better assertion errors (shows actual vs expected)
- Fixtures for setup/teardown
- Parametrization is cleaner

---

## Exercise 5: FastAPI Basics

### Task 5.1: Create Your First Endpoint
Create `learning/exercises/my_api.py`:

```python
from fastapi import FastAPI, HTTPException, Query
from pydantic import BaseModel

app = FastAPI(title="Learning API")

# Simple GET endpoint
@app.get("/")
async def root():
    """Root endpoint."""
    return {"message": "Hello, World!"}

# GET with path parameter
@app.get("/items/{item_id}")
async def get_item(item_id: int):
    """Get item by ID."""
    return {"item_id": item_id, "name": f"Item {item_id}"}

# GET with query parameters
@app.get("/search")
async def search(
    q: str = Query(..., min_length=1, description="Search query"),
    limit: int = Query(10, ge=1, le=100)
):
    """Search with validation."""
    return {
        "query": q,
        "limit": limit,
        "results": [f"Result {i}" for i in range(limit)]
    }

# POST with request body
class CreateItemRequest(BaseModel):
    name: str
    price: float
    quantity: int = 1

@app.post("/items")
async def create_item(item: CreateItemRequest):
    """Create new item."""
    return {
        "id": 123,
        "name": item.name,
        "price": item.price,
        "quantity": item.quantity
    }

# Error handling
@app.get("/items/{item_id}/details")
async def get_item_details(item_id: int):
    """Get item details with error handling."""
    if item_id < 1:
        raise HTTPException(status_code=400, detail="Invalid item ID")
    if item_id > 1000:
        raise HTTPException(status_code=404, detail="Item not found")
    return {"item_id": item_id, "details": "Some details"}
```

**Run it:**
```bash
uvicorn learning.exercises.my_api:app --reload
# Visit: http://localhost:8000/docs (auto-generated docs!)
```

### Task 5.2: Test Your API
Create `learning/exercises/test_my_api.py`:

```python
import pytest
from fastapi.testclient import TestClient
from learning.exercises.my_api import app

@pytest.fixture
def client():
    """Test client fixture."""
    return TestClient(app)

def test_root(client):
    """Test root endpoint."""
    response = client.get("/")
    assert response.status_code == 200
    assert response.json() == {"message": "Hello, World!"}

def test_get_item(client):
    """Test get item endpoint."""
    response = client.get("/items/42")
    assert response.status_code == 200
    data = response.json()
    assert data["item_id"] == 42

def test_search(client):
    """Test search endpoint."""
    response = client.get("/search?q=python&limit=5")
    assert response.status_code == 200
    data = response.json()
    assert data["query"] == "python"
    assert len(data["results"]) == 5

def test_search_validation(client):
    """Test search validation."""
    # Empty query should fail
    response = client.get("/search?q=")
    assert response.status_code == 422  # Validation error

def test_create_item(client):
    """Test create item endpoint."""
    response = client.post("/items", json={
        "name": "Widget",
        "price": 9.99,
        "quantity": 5
    })
    assert response.status_code == 200
    data = response.json()
    assert data["name"] == "Widget"

def test_error_handling(client):
    """Test error responses."""
    response = client.get("/items/-1/details")
    assert response.status_code == 400

    response = client.get("/items/9999/details")
    assert response.status_code == 404
```

**Java Spring Boot Comparison:**
```java
@RestController
@RequestMapping("/api")
public class MyController {

    @GetMapping("/items/{itemId}")
    public ResponseEntity<ItemResponse> getItem(@PathVariable int itemId) {
        if (itemId < 1) {
            throw new ResponseStatusException(HttpStatus.BAD_REQUEST, "Invalid ID");
        }
        return ResponseEntity.ok(new ItemResponse(itemId, "Item " + itemId));
    }

    @PostMapping("/items")
    public ResponseEntity<ItemResponse> createItem(@Valid @RequestBody CreateItemRequest request) {
        // Create item...
        return ResponseEntity.ok(response);
    }
}
```

FastAPI advantages:
- Less boilerplate
- Automatic OpenAPI docs
- Type hints = validation
- Async by default

---

## Exercise 6: Dependency Injection

### Task 6.1: Create a Service
Create `learning/exercises/services.py`:

```python
from typing import Protocol

# Interface (Protocol)
class DatabaseService(Protocol):
    """Database service interface."""
    def get_user(self, user_id: int) -> dict:
        ...

# Implementation
class PostgresDatabase:
    """PostgreSQL database implementation."""

    def __init__(self, connection_string: str):
        self.connection_string = connection_string
        print(f"Connected to: {connection_string}")

    def get_user(self, user_id: int) -> dict:
        # Simulate database query
        return {
            "id": user_id,
            "name": f"User {user_id}",
            "email": f"user{user_id}@example.com"
        }

# Factory function
def make_database() -> DatabaseService:
    """Create database service."""
    return PostgresDatabase("postgresql://localhost:5432/mydb")
```

### Task 6.2: Use Dependency Injection
Create `learning/exercises/di_api.py`:

```python
from fastapi import FastAPI, Depends
from learning.exercises.services import DatabaseService, make_database

app = FastAPI()

# Dependency function
def get_database() -> DatabaseService:
    """Get database dependency."""
    return make_database()

# Use dependency in endpoint
@app.get("/users/{user_id}")
async def get_user(
    user_id: int,
    db: DatabaseService = Depends(get_database)  # Inject dependency
):
    """Get user - database is injected automatically."""
    user = db.get_user(user_id)
    return user

# Dependency can be reused
@app.get("/users/{user_id}/profile")
async def get_user_profile(
    user_id: int,
    db: DatabaseService = Depends(get_database)
):
    """Get user profile - same database injected."""
    user = db.get_user(user_id)
    return {"user": user, "profile": "Some profile data"}
```

**Compare to project:**
Study `src/dependencies.py` to see how the project uses DI:

```python
# src/dependencies.py
def get_opensearch_client(request: Request) -> OpenSearchClient:
    """Inject OpenSearch client from app state."""
    return request.app.state.opensearch_client

# Used in routers
@router.get("/search")
async def search(
    opensearch: OpenSearchClient = Depends(get_opensearch_client)
):
    # opensearch is injected automatically
    results = await opensearch.search(query)
    return results
```

**Java Spring Comparison:**
```java
@Service
public class UserService {
    private final DatabaseService database;

    @Autowired  // Dependency injection
    public UserService(DatabaseService database) {
        this.database = database;
    }

    public User getUser(int userId) {
        return database.getUser(userId);
    }
}

@RestController
public class UserController {
    private final UserService userService;

    @Autowired
    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping("/users/{userId}")
    public User getUser(@PathVariable int userId) {
        return userService.getUser(userId);
    }
}
```

FastAPI DI differences:
- Function-based (not annotation-based)
- More explicit (you see what's injected)
- No magic framework scanning
- Can inject into function parameters directly

---

## Challenge Exercises

### Challenge 1: Build a Todo API
Create a complete CRUD API for todos:
- `GET /todos` - List all todos
- `POST /todos` - Create todo
- `GET /todos/{id}` - Get todo by ID
- `PUT /todos/{id}` - Update todo
- `DELETE /todos/{id}` - Delete todo

Use:
- Pydantic models for validation
- In-memory storage (dict)
- Proper error handling
- Tests for all endpoints

### Challenge 2: Add Database
Extend Challenge 1 with SQLAlchemy:
- Create `Todo` ORM model
- Add repository layer
- Use dependency injection
- Add database migrations (Alembic)

### Challenge 3: Add Search
Extend Challenge 2 with search:
- Filter todos by status
- Search todos by text
- Pagination support
- Sort by created_at

---

## Study Checklist

After completing Phase 1, you should be able to:

- [ ] Write Python classes with type hints
- [ ] Use list/dict comprehensions instead of loops
- [ ] Understand the difference between lists, dicts, sets, tuples
- [ ] Create Pydantic models with validation
- [ ] Write async functions with async/await
- [ ] Create FastAPI endpoints with path/query parameters
- [ ] Test APIs with pytest and TestClient
- [ ] Use dependency injection in FastAPI
- [ ] Understand Python's import system
- [ ] Debug Python code effectively

---

## Next Steps

Once you complete these exercises:
1. Review your code with `make lint` and `make format`
2. Write tests for all your code
3. Move to Phase 2: Project Architecture Deep Dive
4. Start reading the actual project code with confidence!

Remember: **Learn by doing!** Don't just read - type the code, run it, break it, fix it.
