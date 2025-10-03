# Claude Code Prompts for Learning

This file contains useful prompts to use with Claude Code (claude.ai/code) while working through the learning plan.

---

## Phase 1: Python Fundamentals

### Understanding Code Comparisons
```
I'm a Java developer learning Python. Can you explain this Python code and compare it to equivalent Java code?

[paste Python code here]
```

### Code Review
```
I wrote this Python code as a Java developer. Can you review it and point out:
1. Any "Java-isms" I should change to be more Pythonic
2. Python idioms I should use instead
3. Type hints I'm missing

[paste your code here]
```

### Concept Explanation
```
I'm familiar with [Java concept] in Java. How does [Python concept] work in Python and what are the key differences?

For example:
- Java interfaces vs Python Protocols
- Java streams vs Python comprehensions
- Java @Autowired vs FastAPI Depends()
```

### Error Debugging
```
I'm getting this error when running my Python code:

[paste error here]

I come from Java - can you explain what this error means and how to fix it?
```

---

## Phase 2: Project Architecture

### Understanding Project Structure
```
Can you explain how the arxiv-paper-curator project is structured? Specifically:
1. What is the role of [file/directory]?
2. How does [component A] interact with [component B]?
3. Where should I add code for [new feature]?
```

### Tracing Request Flow
```
Can you trace how a request flows through the system for this endpoint?

[paste endpoint code or URL]

Show me:
1. Which router handles it
2. What dependencies are injected
3. Which services are called
4. What database queries run
```

### Understanding Dependencies
```
I'm looking at src/dependencies.py. Can you explain:
1. How FastAPI dependency injection works
2. How this compares to Spring's @Autowired
3. How to create a new dependency for [my use case]
```

### Database Queries
```
Can you explain this SQLAlchemy query and show me the equivalent SQL?

[paste SQLAlchemy code]

Also show me how this would look in Java with JPA/Hibernate.
```

---

## Phase 3: RAG Fundamentals

### Understanding RAG Pipeline
```
Can you explain how the RAG pipeline works in this project?

Walk me through what happens when a user asks: "[example question]"

Include:
1. Search/retrieval step
2. Context assembly
3. Prompt construction
4. LLM generation
5. Response streaming
```

### Search Concepts
```
I'm trying to understand [BM25 / vector search / hybrid search]. Can you:
1. Explain the concept in simple terms
2. Show me where it's implemented in the codebase
3. Explain when to use it vs alternatives
4. Show me example query results
```

### Embeddings Deep Dive
```
Can you explain how text embeddings work in this project?

1. What is an embedding?
2. How does Jina AI generate them?
3. How are they stored in OpenSearch?
4. How is similarity calculated?
5. Show me the code that handles this
```

### Prompt Engineering
```
I want to understand the prompt engineering in src/services/ollama/prompts.py

Can you:
1. Explain the current prompt structure
2. Show me why each part is important
3. Suggest improvements for [specific use case]
4. Explain prompt engineering best practices
```

---

## Phase 4: Advanced Topics

### Performance Optimization
```
I want to optimize [specific component]. Can you:
1. Profile the current performance
2. Identify bottlenecks
3. Suggest optimizations
4. Help implement and test improvements
```

### Caching Strategy
```
Can you explain the caching strategy in this project?

1. How does Redis caching work?
2. What is the cache key strategy?
3. When should I use caching?
4. How do I add caching to [new endpoint]?
```

### Async Best Practices
```
I'm writing an async function that [does X]. Can you review it and ensure:
1. I'm using async/await correctly
2. I'm not blocking the event loop
3. I'm handling concurrent operations efficiently
4. Error handling is proper

[paste code]
```

### Testing Strategy
```
I need to test [component/endpoint]. Can you help me:
1. Write unit tests
2. Write integration tests
3. Set up test fixtures
4. Mock external dependencies

Here's what I want to test: [description]
```

---

## Learning & Code Understanding

### Explaining Complex Code
```
I'm looking at [file path] but don't understand what's happening. Can you:
1. Explain what this code does in simple terms
2. Break down the complex parts
3. Show me example inputs/outputs
4. Compare to Java equivalents where applicable
```

### Feature Implementation
```
I want to implement [feature description]. Can you:
1. Break it down into steps
2. Identify which files I need to modify
3. Show me code examples
4. Help me write tests
5. Review my implementation

Coming from Java, are there Python-specific patterns I should use?
```

### Debugging Help
```
I'm seeing [unexpected behavior] when I [do action]. Can you help me:
1. Identify where the bug might be
2. Add logging/debugging to narrow it down
3. Understand what's going wrong
4. Fix the issue
5. Add tests to prevent regression
```

### Best Practices
```
What are Python/FastAPI/RAG best practices for [specific topic]?

Show me:
1. The recommended approach
2. Why it's better than alternatives
3. Examples from this project
4. How to apply it to my code
```

---

## Project-Specific Prompts

### Understanding Services
```
Can you explain the [service name] service?

File: src/services/[service]/client.py

1. What does it do?
2. What external services does it call?
3. How is it used in the project?
4. How can I extend it for [use case]?
```

### Understanding Routers
```
Can you explain the [router name] router?

File: src/routers/[router].py

1. What endpoints does it expose?
2. What request/response models does it use?
3. What business logic does it implement?
4. How can I add a new endpoint for [feature]?
```

### Understanding Data Flow
```
Can you trace how data flows from [source] to [destination]?

For example:
- PDF → Chunks → OpenSearch
- User query → Search → LLM → Response
- arXiv API → Database → API response
```

### Understanding Configuration
```
Can you explain how configuration works in this project?

1. How are environment variables loaded?
2. What settings are available?
3. How do I add a new configuration option?
4. How does this compare to Spring's @ConfigurationProperties?
```

---

## Hands-On Learning Prompts

### Guided Tutorial
```
Can you give me a step-by-step tutorial on [topic]?

Include:
1. Concepts I need to understand
2. Code examples I can run
3. Exercises to practice
4. How to verify I understand it

I'm a Java developer, so please compare to Java where helpful.
```

### Code Exercise
```
Can you create a coding exercise for me to practice [skill]?

Requirements:
1. Clear problem description
2. Example test cases
3. Hints if I get stuck
4. Solution with explanation
5. Extension challenges

Related to this project if possible.
```

### Code Review & Improvement
```
I implemented [feature]. Can you review my code and help me improve it?

[paste code]

Please check:
1. Pythonic style
2. Type hints
3. Error handling
4. Tests
5. Documentation
6. Performance
```

---

## RAG-Specific Learning

### Understanding Search Results
```
I ran this search query: "[query]"

Can you:
1. Show me what OpenSearch returns
2. Explain the relevance scores
3. Compare BM25 vs vector vs hybrid results
4. Explain why certain results ranked higher
```

### Understanding Prompts
```
I'm looking at this prompt:

[paste prompt]

Can you:
1. Explain each section's purpose
2. Show me how context is injected
3. Explain how to improve it for [use case]
4. Show me the LLM's response
```

### Understanding Document Processing
```
Can you walk me through how a PDF becomes searchable?

1. PDF parsing (Docling)
2. Text extraction
3. Chunking strategy
4. Embedding generation
5. OpenSearch indexing

Show me the relevant code for each step.
```

### Understanding Airflow DAG
```
Can you explain the arxiv_paper_ingestion DAG?

1. What tasks does it run?
2. In what order?
3. What does each task do?
4. How do I add a new task?
5. How do I test it?
```

---

## Common Scenarios

### "I Want to Add a New Endpoint"
```
I want to add a new endpoint: [describe endpoint]

Can you help me:
1. Create the router
2. Define request/response models
3. Implement business logic
4. Add dependencies
5. Write tests
6. Update API docs
```

### "I Want to Add a New Service"
```
I want to create a new service that [does X]

Can you guide me through:
1. Creating the service class
2. Setting up the factory
3. Adding dependency injection
4. Writing tests
5. Using it in endpoints
```

### "I Want to Modify the RAG Pipeline"
```
I want to change the RAG pipeline to [modification]

Can you:
1. Show me the current implementation
2. Identify what needs to change
3. Help me implement the changes
4. Update tests
5. Measure performance impact
```

### "I Want to Understand Performance"
```
I want to understand why [operation] is slow

Can you:
1. Profile the current performance
2. Identify bottlenecks
3. Explain what's causing slowness
4. Suggest optimizations
5. Help implement improvements
```

---

## Learning Plan Progress

### Phase Check-In
```
I just completed [Phase X]. Can you:
1. Quiz me on key concepts
2. Suggest exercises to reinforce learning
3. Identify knowledge gaps
4. Recommend what to focus on next
```

### Concept Verification
```
I think I understand [concept]. Let me explain it:

[your explanation]

Can you:
1. Verify my understanding is correct
2. Correct any misconceptions
3. Add details I'm missing
4. Show me how it's used in the project
```

### Project Milestone
```
I just completed [project/exercise]. Can you:
1. Review my implementation
2. Compare to best practices
3. Suggest improvements
4. Give me the next challenge
```

---

## Tips for Effective Prompting

1. **Be Specific**: Instead of "explain FastAPI", say "explain FastAPI dependency injection and compare to Spring Boot"

2. **Provide Context**: Mention you're a Java developer learning Python/RAG

3. **Ask for Comparisons**: "How is this different from Java?" helps bridge knowledge

4. **Request Examples**: "Show me code examples from this project"

5. **Break Down Complex Topics**: Ask about one concept at a time

6. **Ask for Step-by-Step**: Request tutorials, walkthroughs, and guided exercises

7. **Verify Understanding**: Explain back what you learned and ask for validation

8. **Request Hands-On**: Ask for exercises and challenges, not just explanations

9. **Link to Project**: "How is this used in arxiv-paper-curator?"

10. **Progressive Learning**: Start simple, gradually increase complexity

---

## Example Learning Session

Here's an example conversation flow:

```
You: I'm a Java developer. Can you explain Python's list comprehensions and compare to Java streams?

Claude: [explains comprehensions vs streams]

You: That makes sense. Can you show me examples from this project where list comprehensions are used?

Claude: [shows examples from codebase]

You: I want to practice. Can you give me an exercise?

Claude: [provides exercise]

You: Here's my solution: [paste code]

Claude: [reviews and provides feedback]

You: Thanks! Now I want to understand how this applies to the search results processing in src/routers/hybrid_search.py

Claude: [explains real usage in project]
```

This progressive approach:
1. ✅ Learns concept
2. ✅ Sees real examples
3. ✅ Practices with exercise
4. ✅ Gets feedback
5. ✅ Applies to actual project

---

## Remember

- **Claude Code is your pair programmer** - use it actively while coding
- **Ask follow-up questions** - dig deeper when something isn't clear
- **Request code reviews** - get feedback on your implementations
- **Learn by doing** - ask for exercises, not just explanations
- **Connect to Java knowledge** - leverage what you already know
- **Focus on this project** - use arxiv-paper-curator as your learning ground

Happy learning! 🚀
