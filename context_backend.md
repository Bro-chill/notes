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
- **Autonomy**: Independent decision-making within scope
- **Reactivity**: Respond to environmental changes
- **Proactivity**: Take initiative to achieve goals
- **Social Ability**: Communicate and collaborate
- **Statefulness**: Maintain context across interactions

### Orchestration Patterns
- **Supervisor**: Single agent coordinates sub-agents
- **Pipeline**: Sequential processing
- **Hub-and-Spoke**: Central orchestrator with specialized agents
- **Peer-to-Peer**: Direct agent communication
- **Hierarchical**: Multi-level organization

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
├── payments/           # Payment gateway integration
│   ├── stripe/         # Stripe-specific implementation
│   ├── webhooks/       # Payment webhook handlers
│   └── schemas/        # Payment schemas
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

### TDD: Write test first → Watch fail → Minimal code → Refactor → Repeat

### Test Types
- **Unit**: Individual functions/methods (pytest)
- **Integration**: Component interactions, database
- **Agent**: Behavior, decision-making, state transitions
- **API**: Endpoints, validation, error handling
- **E2E**: Complete workflows

### Coverage Goals
- **80%+** overall, **100%** for agent decision logic
- Keep test files next to code or in `__tests__/` directories

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

-- Payment-related fields
payment_id, transaction_id, stripe_customer_id, stripe_subscription_id
amount_cents, currency_code, payment_status, subscription_status
```

### Payment-Related Tables

```sql
-- Payments table
payments.payment_id UUID PRIMARY KEY
payments.user_id UUID REFERENCES users(user_id)
payments.stripe_payment_intent_id VARCHAR
payments.amount_cents INTEGER
payments.currency_code VARCHAR(3)
payments.payment_status VARCHAR
payments.created_at TIMESTAMP
payments.updated_at TIMESTAMP

-- Subscriptions table
subscriptions.subscription_id UUID PRIMARY KEY
subscriptions.user_id UUID REFERENCES users(user_id)
subscriptions.stripe_subscription_id VARCHAR
subscriptions.stripe_customer_id VARCHAR
subscriptions.plan_id UUID REFERENCES plans(plan_id)
subscriptions.subscription_status VARCHAR
subscriptions.current_period_start TIMESTAMP
subscriptions.current_period_end TIMESTAMP
subscriptions.cancel_at TIMESTAMP
subscriptions.created_at TIMESTAMP
subscriptions.updated_at TIMESTAMP
```

## 🌐 API Route Standards

### RESTful Patterns (FastAPI)
```python
# CRUD: GET/POST/PUT/PATCH/DELETE /api/v1/{resource}
# Sub-resources: /api/v1/{resource}/{id}/{sub-resource}
# Actions: POST /api/v1/{resource}/{id}/{action}
# WebSocket: /ws/{resource}/{id}
# Payments: /api/v1/payments/* and /api/v1/subscriptions/*
# Webhooks: /api/v1/webhooks/stripe
```

### GraphQL (Optional for complex queries)
```python
import strawberry
from strawberry.fastapi import GraphQLRouter
# Use for complex data fetching needs
```

## 📝 Documentation Standards

- **Modules**: Docstring explaining purpose
- **Functions**: Complete docstrings (Google/NumPy style) with Args, Returns, Raises
- **Complex logic**: Inline comments with `# Reason:` prefix
- **Type hints**: All parameters and return values

## 💳 Payment Gateway Integration (Stripe)

### Structure
- **Client**: `payments/stripe/client.py` - Wrapper for Stripe API
- **Schemas**: `payments/schemas.py` - Pydantic models (PaymentStatus, SubscriptionStatus)
- **Routes**: `api/routes/payments.py` - Payment/subscription endpoints
- **Webhooks**: `api/routes/webhooks.py` - Handle Stripe events
- **Service**: `core/services/payment_service.py` - Business logic

### Key Operations
- Create payment intent, customer, subscription
- Handle webhooks with signature verification
- Record transactions in database
- Monitor with Prometheus metrics

### Security Best Practices
- Never store card details (PCI compliance)
- Always verify webhook signatures
- Use idempotency keys
- Validate amounts server-side
- Use test mode in development

## 🔄 Agent Memory & Context Management

### Memory Types
- **Short-term**: Recent conversation (e.g., last 10 messages)
- **Long-term**: Persistent knowledge (vector store)
- **Working**: Current task context, goals, variables

### Context Window Management
- Prioritize: System prompt → Recent messages → Relevant docs → Summarized history
- Implement token limits and pruning
- Use vector similarity for relevant document retrieval

## 🛡️ Security Best Practices

### General
- Never commit secrets (use env vars, AWS Secrets Manager, Vault)
- Validate all input (Pydantic models)
- Parameterized queries (prevent SQL injection)
- Rate limiting (slowapi)
- HTTPS, proper auth (JWT, OAuth2, API keys)
- Audit logging

### Agent-Specific
- Sanitize LLM inputs/outputs (prevent prompt injection)
- Agent sandboxing (limit tools, execution time, memory, domains)
- Input validation (check patterns, length, sensitive keywords)

## ⚡ Performance Optimization

### Database
- Connection pooling, indexes on frequent queries
- Avoid N+1 queries (use joins/prefetch)
- Pagination, query caching

### API
- async/await for I/O
- Response caching (Redis)
- Background tasks for long operations
- Request/response compression (gzip)
- APM monitoring (DataDog, New Relic)

### Agents
- Cache LLM responses
- Streaming for long outputs
- Timeout mechanisms
- Batch API calls
- Use cheaper models for simple tasks

## 🔧 Error Handling & Resilience

### Custom Exception Hierarchy
- Base: `ApplicationError(message, code, details)`
- Specific: `AgentError`, `AgentTimeoutError`, etc.
- Global handler: Return JSON with error code, message, details

### Resilience Patterns
- **Retry**: Exponential backoff with tenacity (3 attempts, 4-10s wait)
- **Circuit Breaker**: Prevent cascading failures (threshold, timeout, states: closed/open/half-open)

## 🚀 Deployment & DevOps

### Environment Configuration (Pydantic Settings)
- App: name, debug
- Database: url, pool_size
- API Keys: OpenAI, Anthropic
- Stripe: api_key, webhook_secret, publishable_key
- Redis: url
- Security: secret_key, token_expire_minutes

### Docker
- **Dockerfile**: Python 3.11-slim, non-root user, uvicorn
- **Compose**: Backend, PostgreSQL 15, Redis 7
- Use volumes for data persistence

## 📊 Monitoring & Observability

### Structured Logging (structlog)
- Levels: debug, info, warning, error, critical
- Include context: agent_id, task_type, user_id

### Metrics (Prometheus)
- **Counter**: agent_requests_total, payment_attempts_total
- **Histogram**: agent_duration_seconds, payment_amount_cents
- **Gauge**: active_agents

### Health Checks
- `/health`: Basic health status
- `/health/ready`: Check dependencies (DB, Redis)

## 🧩 Common Patterns

- **Repository**: Encapsulate data access (get_by_id, save, update)
- **Service Layer**: Business logic using repositories and external services
- **Factory**: Create agents based on type (research, analysis, coding)

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

### Payment Gateway
- **Stripe API**: https://stripe.com/docs/api
- **Stripe Python SDK**: https://stripe.com/docs/api/python
- **Stripe Webhooks**: https://stripe.com/docs/webhooks
- **Stripe Testing**: https://stripe.com/docs/testing

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
