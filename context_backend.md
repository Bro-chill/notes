# CONTEXT_BACKEND.md

This file provides comprehensive guidance to AI Assistant when working with backend and agent development in this repository.

## Core Development Philosophy

### KISS (Keep It Simple, Stupid)

Simplicity should be a key goal in design. Choose straightforward solutions over complex ones whenever possible. Simple solutions are easier to understand, maintain, and debug.

### YAGNI (You Aren't Gonna Need It)

Avoid building functionality on speculation. Implement features only when they are needed, not when you anticipate they might be useful in the future.

### Design Principles

- **Dependency Inversion**: High-level modules should not depend on low-level modules. Both should depend on abstractions.
- **Open/Closed Principle**: Software entities should be open for extension but closed for modification.
- **Single Responsibility**: Each function, class, and module should have one clear purpose.
- **Fail Fast**: Check for potential errors early and raise exceptions immediately when issues occur.
- **Separation of Concerns**: Keep business logic, presentation, and data access layers distinct.
- **Composition over Inheritance**: Favor object composition over class inheritance for code reuse.

## 🤖 Agentic Architecture Patterns

### Agent Design Principles

- **Autonomy**: Agents should make decisions independently within their scope
- **Reactivity**: Agents respond to environmental changes and events
- **Proactivity**: Agents take initiative to achieve goals
- **Social Ability**: Agents can communicate and collaborate with other agents
- **Statefulness**: Agents maintain context across interactions

### Agent Communication Patterns

```python
# Event-driven communication
class AgentEvent:
    event_type: str
    payload: dict
    source_agent_id: str
    timestamp: datetime

# Message passing
class AgentMessage:
    sender_id: str
    receiver_id: str
    content: dict
    metadata: dict
```

### State Management for Agents

```python
# State persistence patterns
class AgentState:
    """Base state for all agents"""
    agent_id: str
    current_task: Optional[Task]
    memory: dict  # Short-term memory
    knowledge_base: dict  # Long-term memory
    goals: list[Goal]
    
# State transitions
class StateTransition:
    from_state: str
    to_state: str
    trigger: str
    conditions: list[Callable]
```

### Agent Orchestration

- **Supervisor Pattern**: Single agent coordinates multiple sub-agents
- **Pipeline Pattern**: Sequential processing through multiple agents
- **Hub-and-Spoke**: Central orchestrator with specialized agents
- **Peer-to-Peer**: Agents communicate directly without central control
- **Hierarchical**: Multi-level agent organization

## 🧱 Code Structure & Modularity

### File and Function Limits

- **Never create a file longer than 1000 lines of code**. If approaching this limit, refactor by splitting into modules.
- **Functions should be under 50 lines** with a single, clear responsibility.
- **Classes should be under 100 lines** and represent a single concept or entity.
- **Organize code into clearly separated modules**, grouped by feature or responsibility.

### Backend Project Structure

```
backend/
├── agents/              # Agent definitions and orchestration
│   ├── base/           # Base agent classes
│   ├── specialized/    # Specialized agent implementations
│   └── orchestrator/   # Agent coordination logic
├── api/                # API endpoints
│   ├── routes/         # Route handlers
│   ├── middleware/     # Custom middleware
│   └── validators/     # Request/response validation
├── core/               # Core business logic
│   ├── services/       # Business services
│   ├── models/         # Data models
│   └── schemas/        # Pydantic schemas
├── database/           # Database layer
│   ├── repositories/   # Data access patterns
│   ├── migrations/     # DB migrations
│   └── seeds/          # Seed data
├── utils/              # Utility functions
├── config/             # Configuration management
└── tests/              # Test suite
```

## 📋 Style & Conventions

### Python Style Guide

- **Follow PEP8** with these specific choices:
  - Line length: 100 characters
  - Use double quotes for strings
  - Use trailing commas in multi-line structures

### Naming Conventions

- **Variables and functions**: `snake_case`
- **Classes**: `PascalCase`
- **Constants**: `UPPER_SNAKE_CASE`
- **Private attributes/methods**: `_leading_underscore`
- **Type aliases**: `PascalCase`
- **Enum values**: `UPPER_SNAKE_CASE`

### Configuration Files
- **Use consistent formatting**: Black for Python
- **Environment variables**: `UPPER_SNAKE_CASE`
- **Config keys**: `snake_case` for Python

## 🧪 Testing Strategy

### Test-Driven Development (TDD)

1. **Write the test first** - Define expected behavior before implementation
2. **Watch it fail** - Ensure the test actually tests something
3. **Write minimal code** - Just enough to make the test pass
4. **Refactor** - Improve code while keeping tests green
5. **Repeat** - One test at a time

### Test Organization

- **Unit tests**: Test individual functions/methods in isolation using `pytest`
- **Integration tests**: Test component interactions, database operations
- **Agent tests**: Test agent behavior, decision-making, and state transitions
- **API tests**: Test endpoint responses, validation, error handling
- **E2E tests**: Test complete workflows with real dependencies

```python
# Example: Agent unit test
def test_agent_decision_making():
    agent = MyAgent(config)
    result = agent.decide(context)
    assert result.action == "expected_action"
    assert result.confidence > 0.8
```

### Test Coverage Goals
- Aim for **80%+ code coverage**, but focus on critical paths
- **100% coverage** for agent decision logic and state machines
- Keep test files next to the code they test or in parallel `__tests__` directories

## 🗄️ Database Naming Standards

### Entity-Specific Primary Keys
All database tables use entity-specific primary keys for clarity and consistency:

```sql
-- ✅ STANDARDIZED: Entity-specific primary keys
users.user_id UUID PRIMARY KEY
sessions.session_id UUID PRIMARY KEY
agents.agent_id UUID PRIMARY KEY
tasks.task_id UUID PRIMARY KEY
conversations.conversation_id UUID PRIMARY KEY
messages.message_id UUID PRIMARY KEY
```

### Field Naming Conventions

```sql
-- Primary keys: {entity}_id
user_id, agent_id, task_id, conversation_id

-- Foreign keys: {referenced_entity}_id
user_id REFERENCES users(user_id)
agent_id REFERENCES agents(agent_id)
conversation_id REFERENCES conversations(conversation_id)

-- Timestamps: {action}_at
created_at, updated_at, started_at, completed_at, expires_at

-- Booleans: is_{state} or has_{property}
is_active, is_completed, has_error, is_archived

-- Counts: {entity}_count
message_count, task_count, retry_count

-- Durations: {property}_{unit}
duration_seconds, timeout_minutes, ttl_hours

-- JSON/JSONB fields: {descriptor}_data or {descriptor}_metadata
agent_config, task_metadata, error_details

-- Arrays: {entity}_list or {entity}s
tag_list, agent_ids, capabilities
```

## 🌐 API Route Standards

### RESTful API Patterns (FastAPI)

```python
# ✅ STANDARDIZED: FastAPI with consistent parameter naming
from fastapi import FastAPI, HTTPException, Depends
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI(
    title="Agentic Application API",
    description="API for managing agents, tasks, and conversations",
    version="1.0.0",
)

# CORS middleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # Configure appropriately for production
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# Resource routes (CRUD operations)
@app.get("/api/v1/agents")                      # GET all agents
@app.post("/api/v1/agents")                     # CREATE new agent
@app.get("/api/v1/agents/{agent_id}")           # GET specific agent
@app.put("/api/v1/agents/{agent_id}")           # UPDATE agent
@app.patch("/api/v1/agents/{agent_id}")         # PARTIAL UPDATE agent
@app.delete("/api/v1/agents/{agent_id}")        # DELETE agent

# Sub-resources
@app.get("/api/v1/agents/{agent_id}/tasks")     # GET agent tasks
@app.post("/api/v1/agents/{agent_id}/tasks")    # CREATE task for agent

# Actions (non-CRUD operations)
@app.post("/api/v1/agents/{agent_id}/execute")  # Execute agent action
@app.post("/api/v1/agents/{agent_id}/pause")    # Pause agent
@app.post("/api/v1/agents/{agent_id}/resume")   # Resume agent

# WebSocket for real-time communication
@app.websocket("/ws/agents/{agent_id}")
async def agent_websocket(websocket: WebSocket, agent_id: str):
    await websocket.accept()
    # Handle real-time agent communication
```

### GraphQL API Patterns (Optional)

```python
# For complex data fetching needs
import strawberry
from strawberry.fastapi import GraphQLRouter

@strawberry.type
class Agent:
    agent_id: str
    name: str
    status: str
    tasks: list["Task"]

@strawberry.type
class Query:
    @strawberry.field
    def agent(self, agent_id: str) -> Agent:
        return get_agent(agent_id)

schema = strawberry.Schema(query=Query)
graphql_app = GraphQLRouter(schema)
app.include_router(graphql_app, prefix="/graphql")
```

## 📝 Documentation Standards

### Code Documentation

- Every module should have a docstring explaining its purpose
- Public functions must have complete docstrings (Google or NumPy style)
- Complex logic should have inline comments with `# Reason:` prefix
- Type hints for all function parameters and return values

```python
def process_agent_task(
    agent_id: str,
    task_data: dict,
    timeout: int = 300
) -> TaskResult:
    """
    Process a task for a specific agent.
    
    Args:
        agent_id: Unique identifier for the agent
        task_data: Dictionary containing task parameters
        timeout: Maximum execution time in seconds
        
    Returns:
        TaskResult object with execution details
        
    Raises:
        AgentNotFoundError: If agent_id doesn't exist
        TaskTimeoutError: If execution exceeds timeout
    """
    pass
```

## 🔄 Agent Memory & Context Management

### Memory Types

```python
# Short-term memory (conversation context)
class ShortTermMemory:
    max_messages: int = 10
    messages: list[Message]
    
    def add_message(self, message: Message) -> None:
        self.messages.append(message)
        if len(self.messages) > self.max_messages:
            self.messages.pop(0)

# Long-term memory (persistent knowledge)
class LongTermMemory:
    vector_store: VectorStore
    
    def store(self, content: str, metadata: dict) -> None:
        embedding = self.embed(content)
        self.vector_store.add(embedding, content, metadata)
    
    def retrieve(self, query: str, k: int = 5) -> list[Document]:
        return self.vector_store.similarity_search(query, k=k)

# Working memory (current task context)
class WorkingMemory:
    current_goal: Goal
    sub_goals: list[Goal]
    context_variables: dict
    tool_outputs: dict
```

### Context Window Management

```python
class ContextManager:
    """Manage token limits and context pruning"""
    
    def __init__(self, max_tokens: int = 4000):
        self.max_tokens = max_tokens
    
    def optimize_context(
        self,
        system_prompt: str,
        conversation: list[Message],
        documents: list[Document]
    ) -> tuple[str, list[Message], list[Document]]:
        """
        Prioritize and prune context to fit within token limits
        
        Priority order:
        1. System prompt (always included)
        2. Recent messages (last N)
        3. Relevant documents (top K by relevance)
        4. Older messages (summarized if needed)
        """
        pass
```

## 🛡️ Security Best Practices

### Backend Security

- **Never commit secrets** - use environment variables and secret management tools (AWS Secrets Manager, Vault)
- **Validate all user input** with Pydantic models and custom validators
- **Use parameterized queries** for all database operations (prevent SQL injection)
- **Implement rate limiting** for all API endpoints (e.g., `slowapi`)
- **Use HTTPS** for all external communications
- **Implement proper authentication and authorization** (JWT, OAuth2, API keys)
- **Sanitize LLM inputs/outputs** - prevent prompt injection and data leakage
- **Agent sandboxing** - limit agent capabilities and resource access
- **Audit logging** - track all agent actions and decisions

### Agent-Specific Security

```python
# Limit agent tool access
class AgentSandbox:
    allowed_tools: list[str]
    max_execution_time: int
    max_memory_mb: int
    allowed_domains: list[str]  # For web access
    
# Input validation for agents
class AgentInputValidator:
    def validate_prompt(self, prompt: str) -> bool:
        # Check for prompt injection patterns
        # Limit prompt length
        # Filter sensitive keywords
        pass
```

## ⚡ Performance Optimization

### Database Optimization
- Use **connection pooling** for database connections
- Add **indexes** on frequently queried columns
- Use **select_related** and **prefetch_related** to avoid N+1 queries
- Implement **pagination** for large datasets
- Use **database query caching** for expensive queries

```python
# Query optimization example
async def get_agent_with_tasks(agent_id: str):
    # ❌ Bad: N+1 query problem
    agent = await db.get_agent(agent_id)
    tasks = await db.get_tasks_by_agent(agent_id)  # Separate query
    
    # ✅ Good: Single query with join
    agent = await db.get_agent_with_tasks(agent_id)
```

### API Performance
- Use **async/await** for I/O operations
- Implement **response caching** with Redis
- Use **background tasks** for long-running operations
- Implement **request/response compression** (gzip)
- Monitor with **APM tools** (DataDog, New Relic)

```python
from fastapi import BackgroundTasks

@app.post("/api/v1/agents/{agent_id}/execute")
async def execute_agent(
    agent_id: str,
    task_data: dict,
    background_tasks: BackgroundTasks
):
    # Return immediately, process in background
    background_tasks.add_task(process_agent_task, agent_id, task_data)
    return {"status": "queued", "task_id": task_id}
```

### Agent Performance
- **Cache LLM responses** for identical prompts
- Use **streaming responses** for long outputs
- Implement **timeout mechanisms** for agent operations
- **Batch API calls** when possible
- Use **cheaper models** for simple tasks

## 🔧 Error Handling & Resilience

### Custom Exception Hierarchy

```python
# Custom exception hierarchy
class ApplicationError(Exception):
    """Base exception for application errors"""
    def __init__(self, message: str, code: str, details: dict = None):
        self.message = message
        self.code = code
        self.details = details or {}

class AgentError(ApplicationError):
    """Agent-specific errors"""
    pass

class AgentTimeoutError(AgentError):
    """Agent execution timeout"""
    pass

# Global error handler
@app.exception_handler(ApplicationError)
async def application_error_handler(request: Request, exc: ApplicationError):
    return JSONResponse(
        status_code=400,
        content={
            "error": exc.code,
            "message": exc.message,
            "details": exc.details
        }
    )
```

### Retry Mechanism

```python
# Retry mechanism with exponential backoff
from tenacity import retry, stop_after_attempt, wait_exponential

@retry(
    stop=stop_after_attempt(3),
    wait=wait_exponential(multiplier=1, min=4, max=10)
)
async def call_external_api(url: str):
    async with httpx.AsyncClient() as client:
        response = await client.get(url)
        response.raise_for_status()
        return response.json()
```

### Circuit Breaker Pattern

```python
class CircuitBreaker:
    """Prevent cascading failures"""
    def __init__(self, failure_threshold: int = 5, timeout: int = 60):
        self.failure_threshold = failure_threshold
        self.timeout = timeout
        self.failures = 0
        self.last_failure_time = None
        self.state = "closed"  # closed, open, half-open
    
    async def call(self, func, *args, **kwargs):
        if self.state == "open":
            if time.time() - self.last_failure_time > self.timeout:
                self.state = "half-open"
            else:
                raise CircuitBreakerOpen()
        
        try:
            result = await func(*args, **kwargs)
            self.on_success()
            return result
        except Exception as e:
            self.on_failure()
            raise
```

## 🚀 Deployment & DevOps

### Environment Configuration

```python
# config/settings.py
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    # Application
    app_name: str = "Agentic App"
    debug: bool = False
    
    # Database
    database_url: str
    db_pool_size: int = 10
    
    # API Keys
    openai_api_key: str
    anthropic_api_key: str | None = None
    
    # Redis
    redis_url: str | None = None
    
    # Security
    secret_key: str
    access_token_expire_minutes: int = 30
    
    class Config:
        env_file = ".env"
        case_sensitive = False

settings = Settings()
```

### Docker Configuration

```dockerfile
# Backend Dockerfile
FROM python:3.11-slim

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application
COPY . .

# Run as non-root user
RUN useradd -m appuser && chown -R appuser:appuser /app
USER appuser

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Docker Compose

```yaml
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/dbname
      - REDIS_URL=redis://redis:6379
    depends_on:
      - db
      - redis
    volumes:
      - ./backend:/app
    
  db:
    image: postgres:15
    environment:
      - POSTGRES_DB=dbname
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
    volumes:
      - postgres_data:/var/lib/postgresql/data
    
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

## 📊 Monitoring & Observability

### Logging Standards

```python
# Structured logging
import structlog

logger = structlog.get_logger()

# Log with context
logger.info(
    "agent_task_started",
    agent_id=agent_id,
    task_type=task_type,
    user_id=user_id
)

# Log levels
logger.debug("Detailed diagnostic information")
logger.info("General informational messages")
logger.warning("Warning messages for potentially harmful situations")
logger.error("Error messages", exc_info=True)
logger.critical("Critical issues requiring immediate attention")
```

### Metrics & Tracing

```python
# Prometheus metrics
from prometheus_client import Counter, Histogram, Gauge

agent_requests = Counter(
    'agent_requests_total',
    'Total agent requests',
    ['agent_type', 'status']
)

agent_duration = Histogram(
    'agent_duration_seconds',
    'Agent execution duration',
    ['agent_type']
)

active_agents = Gauge(
    'active_agents',
    'Number of currently active agents'
)

# Usage
with agent_duration.labels(agent_type='research').time():
    result = await agent.execute()
    agent_requests.labels(agent_type='research', status='success').inc()
```

### Health Checks

```python
@app.get("/health")
async def health_check():
    return {
        "status": "healthy",
        "timestamp": datetime.utcnow().isoformat(),
        "version": "1.0.0"
    }

@app.get("/health/ready")
async def readiness_check():
    # Check dependencies
    db_healthy = await check_database()
    redis_healthy = await check_redis()
    
    if not (db_healthy and redis_healthy):
        raise HTTPException(status_code=503, detail="Service not ready")
    
    return {"status": "ready"}
```

## 🧩 Common Patterns

### Repository Pattern
```python
class AgentRepository:
    def __init__(self, db: Database):
        self.db = db
    
    async def get_by_id(self, agent_id: str) -> Agent | None:
        return await self.db.fetch_one(
            "SELECT * FROM agents WHERE agent_id = :id",
            {"id": agent_id}
        )
    
    async def save(self, agent: Agent) -> Agent:
        # Encapsulate data access logic
        pass
```

### Service Layer Pattern
```python
class AgentService:
    def __init__(self, repo: AgentRepository, llm: LLM):
        self.repo = repo
        self.llm = llm
    
    async def execute_task(self, agent_id: str, task: Task) -> Result:
        agent = await self.repo.get_by_id(agent_id)
        # Business logic here
        result = await agent.process(task, self.llm)
        await self.repo.update_metrics(agent_id, result.metrics)
        return result
```

### Factory Pattern for Agents
```python
class AgentFactory:
    @staticmethod
    def create_agent(agent_type: str, config: dict) -> BaseAgent:
        agents = {
            "research": ResearchAgent,
            "analysis": AnalysisAgent,
            "coding": CodingAgent
        }
        agent_class = agents.get(agent_type)
        if not agent_class:
            raise ValueError(f"Unknown agent type: {agent_type}")
        return agent_class(config)
```

## 📚 Useful Resources

### AI/ML Frameworks
- **LangChain**: https://python.langchain.com/docs/introduction/
- **LangGraph**: https://langchain-ai.github.io/langgraph/reference/
- **LlamaIndex**: https://docs.llamaindex.ai/
- **AutoGen**: https://microsoft.github.io/autogen/
- **CrewAI**: https://docs.crewai.com/

### Python Frameworks
- **FastAPI**: https://fastapi.tiangolo.com/
- **Pydantic**: https://docs.pydantic.dev/
- **SQLAlchemy**: https://docs.sqlalchemy.org/
- **Pytest**: https://docs.pytest.org/

### Vector Databases
- **Pinecone**: https://docs.pinecone.io/
- **Weaviate**: https://weaviate.io/developers/weaviate
- **Qdrant**: https://qdrant.tech/documentation/
- **Chroma**: https://docs.trychroma.com/

### Best Practices
- **PEP 8** (Python): https://pep8.org/
- **PEP 484** (Type Hints): https://www.python.org/dev/peps/pep-0484/
- **The Hitchhiker's Guide to Python**: https://docs.python-guide.org/

## ⚠️ Important Notes

### Development Guidelines

- **NEVER ASSUME OR GUESS** - When in doubt, ask for clarification
- **Always verify file paths and module names** before use
- **Test your code** - No feature is complete without tests
- **Document your decisions** - Future developers (including yourself) will thank you
- **Keep it simple** - Complexity is the enemy of maintainability
- **Security first** - Never compromise on security for convenience

### AI Agent Development

- **Start simple** - Begin with single-agent systems before building multi-agent orchestration
- **Define clear boundaries** - Each agent should have a well-defined purpose and scope
- **Handle failures gracefully** - Agents should degrade gracefully and provide fallbacks
- **Monitor everything** - Track agent performance, costs, and behaviors
- **Version your prompts** - Treat prompts as code; version and test them
- **Validate agent outputs** - Never trust agent outputs blindly; always validate
- **Cost awareness** - Monitor LLM API costs; cache when possible

### Code Review Checklist

- [ ] Code follows style guide and naming conventions
- [ ] All functions have appropriate documentation
- [ ] Tests are included and passing
- [ ] No hardcoded secrets or sensitive data
- [ ] Error handling is comprehensive
- [ ] Performance implications considered
- [ ] Security vulnerabilities addressed
- [ ] Breaking changes documented
- [ ] Database migrations included (if applicable)
- [ ] Dependencies are up to date and secure
