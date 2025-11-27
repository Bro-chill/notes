# CONTEXT.md

This file provides comprehensive guidance to AI Assistant when working with full-stack agentic applications in this repository.

> **Note**: This context has been split into specialized files for better organization:
> - **[context_backend.md](./context_backend.md)** - Backend, API, and Agent development
> - **[context_frontend.md](./context_frontend.md)** - Frontend, React, and UI development

## Quick Reference

For detailed guidance, refer to the specialized context files above. This document provides a high-level overview and shared principles.

## Full-Stack Project Structure

```
project-root/
├── backend/                # See context_backend.md
│   ├── agents/            # Agent definitions and orchestration
│   ├── api/               # API endpoints
│   ├── core/              # Core business logic
│   ├── database/          # Database layer
│   └── tests/             # Backend tests
├── frontend/              # See context_frontend.md
│   ├── src/
│   │   ├── components/    # Reusable UI components
│   │   ├── pages/         # Page components
│   │   ├── hooks/         # Custom React hooks
│   │   ├── services/      # API client services
│   │   └── store/         # State management
│   └── public/            # Static assets
├── shared/                # Shared types/constants
├── scripts/               # Build and deployment scripts
├── docs/                  # Documentation
└── docker/                # Docker configurations
```

## Core Development Philosophy (Shared)

### KISS (Keep It Simple, Stupid)
Simplicity should be a key goal in design. Choose straightforward solutions over complex ones whenever possible.

### YAGNI (You Aren't Gonna Need It)
Avoid building functionality on speculation. Implement features only when needed.

### Key Design Principles
- **Dependency Inversion**: Depend on abstractions, not concretions
- **Open/Closed Principle**: Open for extension, closed for modification
- **Single Responsibility**: One clear purpose per function/class/module
- **Fail Fast**: Check for errors early and raise exceptions immediately
- **Separation of Concerns**: Keep layers distinct (business logic, presentation, data)
- **Composition over Inheritance**: Favor composition for code reuse

## Technology Stack Overview

### Backend Stack
- **Language**: Python 3.11+
- **Framework**: FastAPI
- **Agent Frameworks**: LangChain, LangGraph
- **Database**: Relational (Supabase/PostgreSQL), Non-Relational (MongoDB)
- **Cache**: Redis
- **Payment Gateway**: Stripe
- **Testing**: Pytest
- **Validation**: Pydantic

See [context_backend.md](./context_backend.md) for complete backend guidelines.

### Frontend Stack
- **Language**: TypeScript
- **Framework**: React Router v7
- **Build Tool**: Vite
- **State Management**: React Query + Zustand
- **UI Library**: shadcn/ui, Tailwind CSS
- **Testing**: Vitest, Testing Library, Playwright

See [context_frontend.md](./context_frontend.md) for complete frontend guidelines.

## Naming Conventions Summary

### Backend (Python)
- Variables/Functions: `snake_case`
- Classes: `PascalCase`
- Constants: `UPPER_SNAKE_CASE`
- Private: `_leading_underscore`

### Frontend (TypeScript)
- Variables/Functions: `camelCase`
- Components/Classes: `PascalCase`
- Constants: `UPPER_SNAKE_CASE`
- Files: `kebab-case.ts` or `PascalCase.tsx`

### Database
- Tables: `plural_snake_case` (e.g., `users`, `agents`)
- Primary Keys: `{entity}_id` (e.g., `user_id`, `agent_id`)
- Timestamps: `{action}_at` (e.g., `created_at`, `updated_at`)
- Booleans: `is_{state}` or `has_{property}`

## API Integration Pattern

### Backend API (FastAPI)
```python
@app.get("/api/v1/agents/{agent_id}")
async def get_agent(agent_id: str) -> AgentResponse:
    # Implementation
    pass
```

### Frontend API Client (TypeScript)
```typescript
export const fetchAgent = async (agentId: string): Promise<Agent> => {
  const response = await fetch(`${API_URL}/api/v1/agents/${agentId}`);
  if (!response.ok) throw new Error('Failed to fetch agent');
  return response.json();
};

// Usage with React Query
const useAgent = (agentId: string) => {
  return useQuery({
    queryKey: ['agent', agentId],
    queryFn: () => fetchAgent(agentId),
  });
};
```

## Testing Strategy (Shared)

### Backend Tests (Pytest)
- Unit tests for functions/classes
- Integration tests for database/API
- Agent behavior tests
- Coverage goal: 80%+

### Frontend Tests (Vitest/Playwright)
- Unit tests for utilities/hooks
- Component tests with Testing Library
- E2E tests with Playwright
- Coverage goal: 80%+

## Security Best Practices (Shared)

### Universal Rules
- **Never commit secrets** - use environment variables
- **Validate all inputs** - backend AND frontend
- **Use HTTPS** - all communications encrypted
- **Implement authentication** - JWT, OAuth2, API keys
- **Rate limiting** - prevent abuse
- **Audit logging** - track important actions

### Backend-Specific
- Parameterized queries (prevent SQL injection)
- Agent sandboxing (limit capabilities)
- Input sanitization for LLMs

### Frontend-Specific
- XSS prevention (sanitize user content)
- CSP headers (restrict resource loading)
- Secure token storage (httpOnly cookies preferred)

## Development Workflow

### 1. Plan
- Define clear requirements
- Design API contracts first
- Choose appropriate patterns

### 2. Implement
- Start with backend API endpoints
- Create frontend services/hooks
- Build UI components
- Follow TDD when appropriate

### 3. Test
- Write unit tests
- Add integration tests
- E2E testing for critical flows

### 4. Review
- Code follows style guides
- Tests passing
- Documentation complete
- Security reviewed

### 5. Deploy
- Environment configuration
- Docker containerization
- CI/CD pipeline
- Monitoring setup

## Quick Start Commands

### Backend
```bash
# Setup
python -m venv venv
source venv\Scripts\activate # or venv/bin/activate on mac
pip install -r requirements.txt

# Run
uvicorn api.main:app

# Test
pytest
```

### Frontend
```bash
# Setup
npm install

# Run
npm run dev

# Test
npm test

# Build
npm run build
```

### Docker
```bash
# Full stack
docker-compose up

# Backend only
docker-compose up backend

# Frontend only
docker-compose up frontend
```

## Common Patterns

### Backend: Repository Pattern
Encapsulate data access logic
```python
class AgentRepository:
    async def get_by_id(self, agent_id: str) -> Agent:
        # Database query logic
        pass
```

### Backend: Service Layer
Encapsulate business logic
```python
class AgentService:
    def __init__(self, repo: AgentRepository, llm: LLM):
        self.repo = repo
        self.llm = llm
```

### Frontend: Custom Hooks
Encapsulate component logic
```typescript
const useAgents = () => {
  const { data, isLoading, error } = useQuery(['agents'], fetchAgents);
  return { agents: data, isLoading, error };
};
```

### Frontend: Composition
Build complex UIs from simple components
```typescript
<DashboardLayout>
  <AgentList agents={agents} onSelect={handleSelect} />
  <AgentDetails agent={selectedAgent} />
</DashboardLayout>
```

## Documentation Requirements

### Code Documentation
- **Backend**: Docstrings for all public functions/classes
- **Frontend**: JSDoc for complex functions, TypeScript interfaces for props
- **Both**: Inline comments for complex logic with `# Reason:` or `// Reason:`

### Project Documentation
- **README.md** - Setup and quick start
- **ARCHITECTURE.md** - System design
- **API.md** - API documentation
- **AGENTS.md** - Agent capabilities
- **CHANGELOG.md** - Version history

## Resources

### Backend Resources
See [context_backend.md](./context_backend.md#-useful-resources)

### Frontend Resources  
See [context_frontend.md](./context_frontend.md#-useful-resources)

## Important Notes

### Universal Guidelines
- **NEVER ASSUME OR GUESS** - Ask for clarification
- **Always verify paths** - Before using files/modules
- **Test everything** - No feature is complete without tests
- **Document decisions** - Help future developers
- **Keep it simple** - Complexity is the enemy
- **Security first** - Never compromise security

### When to Use Which Context

**Use context_backend.md when:**
- Building API endpoints
- Creating agent logic
- Database operations
- Payment Gateway
- Backend testing
- Server deployment

**Use context_frontend.md when:**
- Building UI components
- Managing state
- API integration
- Frontend testing
- Client deployment

**Use both when:**
- Setting up full-stack features
- Defining API contracts
- End-to-end testing
- Full deployment pipeline

---

**Remember**: Always refer to the specialized context files ([context_backend.md](./context_backend.md) and [context_frontend.md](./context_frontend.md)) for detailed, specific guidance.
