# CONTEXT_FRONTEND.md

This file provides comprehensive guidance to AI Assistant when working with frontend development for agentic applications in this repository.

## Core Development Philosophy

### KISS (Keep It Simple, Stupid)

Simplicity should be a key goal in design. Choose straightforward solutions over complex ones whenever possible. Simple solutions are easier to understand, maintain, and debug.

### YAGNI (You Aren't Gonna Need It)

Avoid building functionality on speculation. Implement features only when they are needed, not when you anticipate they might be useful in the future.

### Design Principles

- **Dependency Inversion**: High-level modules should not depend on low-level modules. Both should depend on abstractions.
- **Open/Closed Principle**: Software entities should be open for extension but closed for modification.
- **Single Responsibility**: Each function, class, and module should have one clear purpose.
- **Separation of Concerns**: Keep business logic, presentation, and data access layers distinct.
- **Composition over Inheritance**: Favor object composition over class inheritance for code reuse.

## 🧱 Code Structure & Modularity

### File and Component Limits

- **Never create a file longer than 1000 lines of code**. If approaching this limit, refactor by splitting into modules.
- **Functions should be under 50 lines** with a single, clear responsibility.
- **React components should be under 200 lines**; extract sub-components or hooks for complexity.
- **Organize code into clearly separated modules**, grouped by feature or responsibility.

### Frontend Project Structure

```
frontend/
├── src/
│   ├── components/     # Reusable UI components
│   │   ├── common/     # Shared components
│   │   │   ├── Button/
│   │   │   ├── Input/
│   │   │   ├── Modal/
│   │   │   └── LoadingSpinner/
│   │   ├── agent/      # Agent-specific UI
│   │   │   ├── AgentCard/
│   │   │   ├── AgentList/
│   │   │   ├── AgentStatus/
│   │   │   └── AgentChat/
│   │   └── layout/     # Layout components
│   │       ├── Header/
│   │       ├── Sidebar/
│   │       └── DashboardLayout/
│   ├── pages/          # Page components
│   │   ├── HomePage.tsx
│   │   ├── AgentPage.tsx
│   │   ├── TasksPage.tsx
│   │   └── SettingsPage.tsx
│   ├── hooks/          # Custom React hooks
│   │   ├── useAgents.ts
│   │   ├── useWebSocket.ts
│   │   ├── useAuth.ts
│   │   └── useLocalStorage.ts
│   ├── services/       # API client services
│   │   ├── api.ts
│   │   ├── agentService.ts
│   │   ├── taskService.ts
│   │   └── websocketService.ts
│   ├── store/          # State management
│   │   ├── slices/     # Redux slices or Zustand stores
│   │   │   ├── agentSlice.ts
│   │   │   ├── authSlice.ts
│   │   │   └── uiSlice.ts
│   │   └── middleware/ # Store middleware
│   ├── types/          # TypeScript type definitions
│   │   ├── agent.ts
│   │   ├── task.ts
│   │   ├── api.ts
│   │   └── common.ts
│   ├── utils/          # Utility functions
│   │   ├── formatters.ts
│   │   ├── validators.ts
│   │   └── helpers.ts
│   ├── styles/         # Global styles
│   │   ├── globals.css
│   │   ├── variables.css
│   │   └── themes.css
│   ├── config/         # App configuration
│   │   ├── constants.ts
│   │   └── env.ts
│   ├── App.tsx
│   └── main.tsx
└── public/             # Static assets
    ├── images/
    ├── icons/
    └── fonts/
```

## 📋 Style & Conventions

### TypeScript/JavaScript Style Guide

- **Follow Standard JS** with these specific choices:
  - Line length: 100 characters
  - Use single quotes for strings (except JSX)
  - Use semicolons consistently
  - Prefer `const` over `let`, avoid `var`
  - Use arrow functions for callbacks

### Naming Conventions

- **Variables and functions**: `camelCase`
- **Classes and Components**: `PascalCase`
- **Constants**: `UPPER_SNAKE_CASE`
- **Interfaces/Types**: `PascalCase` with optional `I` prefix for interfaces
- **Files**: `kebab-case.ts` for utilities, `PascalCase.tsx` for components
- **CSS/SCSS**: `kebab-case` for classes

### Configuration Files
- **Use consistent formatting**: Prettier for frontend
- **Environment variables**: `VITE_` prefix for Vite, `NEXT_PUBLIC_` for Next.js
- **Config keys**: `camelCase` for JavaScript/JSON

## 🎨 Frontend Architecture

### Component Design Patterns

#### Atomic Design Structure
- **Atoms**: Basic UI elements (buttons, inputs, labels)
- **Molecules**: Simple component groups (search bar, card header)
- **Organisms**: Complex components (navigation, agent dashboard)
- **Templates**: Page layouts without data
- **Pages**: Complete views with data

#### Component Best Practices

```typescript
// Use composition and custom hooks
const AgentDashboard = () => {
  const { agents, loading, error } = useAgents();
  const { selectedAgent, selectAgent } = useAgentSelection();
  
  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorDisplay error={error} />;
  
  return (
    <DashboardLayout>
      <AgentList agents={agents} onSelect={selectAgent} />
      {selectedAgent && <AgentDetails agent={selectedAgent} />}
    </DashboardLayout>
  );
};
```

#### Component Structure Template

```typescript
import React from 'react';
import styles from './ComponentName.module.css';

interface ComponentNameProps {
  /** Brief description of the prop */
  propName: string;
  /** Optional callback */
  onAction?: (id: string) => void;
  /** Optional className for styling */
  className?: string;
}

/**
 * ComponentName displays/handles [description]
 * 
 * @example
 * ```tsx
 * <ComponentName 
 *   propName="value"
 *   onAction={(id) => handleAction(id)}
 * />
 * ```
 */
export const ComponentName: React.FC<ComponentNameProps> = ({
  propName,
  onAction,
  className,
}) => {
  // Hooks at the top
  const [state, setState] = useState(initialValue);
  
  // Event handlers
  const handleClick = useCallback(() => {
    onAction?.(propName);
  }, [propName, onAction]);
  
  // Render
  return (
    <div className={`${styles.container} ${className}`}>
      {/* Component content */}
    </div>
  );
};
```

### State Management

#### Client State (React)
- **Local state**: `useState` for component-specific data
- **Shared state**: Context API for theme, auth, user preferences
- **Complex state**: `useReducer` for state machines and complex logic

```typescript
// Context example for theme
interface ThemeContextType {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export const ThemeProvider: React.FC<{ children: ReactNode }> = ({ children }) => {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');
  
  const toggleTheme = useCallback(() => {
    setTheme(prev => prev === 'light' ? 'dark' : 'light');
  }, []);
  
  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

export const useTheme = () => {
  const context = useContext(ThemeContext);
  if (!context) throw new Error('useTheme must be used within ThemeProvider');
  return context;
};
```

#### Server State (React Query)

**React Query (TanStack Query)**: Recommended for API data fetching, caching, synchronization

```typescript
// React Query example
const useAgentData = (agentId: string) => {
  return useQuery({
    queryKey: ['agent', agentId],
    queryFn: () => fetchAgent(agentId),
    staleTime: 1000 * 60 * 5, // 5 minutes
    refetchInterval: 1000 * 30, // 30 seconds for active agents
    enabled: !!agentId, // Only fetch if agentId exists
  });
};

// Mutations
const useUpdateAgent = () => {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: (data: UpdateAgentData) => updateAgent(data),
    onSuccess: (data, variables) => {
      // Invalidate and refetch
      queryClient.invalidateQueries(['agent', variables.agentId]);
      // Or optimistically update
      queryClient.setQueryData(['agent', variables.agentId], data);
    },
  });
};
```

**SWR**: Alternative for simpler use cases

```typescript
import useSWR from 'swr';

const useAgents = () => {
  const { data, error, isLoading, mutate } = useSWR(
    '/api/v1/agents',
    fetcher,
    {
      refreshInterval: 30000, // 30 seconds
      revalidateOnFocus: true,
    }
  );
  
  return {
    agents: data,
    isLoading,
    error,
    refresh: mutate,
  };
};
```

#### Global State (when needed)

**Zustand**: Lightweight, simple API, recommended for most cases

```typescript
// Zustand store example
interface AgentStore {
  agents: Agent[];
  activeAgentId: string | null;
  setActiveAgent: (id: string) => void;
  updateAgent: (id: string, updates: Partial<Agent>) => void;
}

const useAgentStore = create<AgentStore>((set) => ({
  agents: [],
  activeAgentId: null,
  setActiveAgent: (id) => set({ activeAgentId: id }),
  updateAgent: (id, updates) => set((state) => ({
    agents: state.agents.map(a => a.id === id ? { ...a, ...updates } : a)
  })),
}));

// Usage in components
const AgentSelector = () => {
  const { activeAgentId, setActiveAgent } = useAgentStore();
  // Component logic
};
```

**Redux Toolkit**: For complex applications with extensive state logic

```typescript
// Redux Toolkit slice
import { createSlice, PayloadAction } from '@reduxjs/toolkit';

interface AgentState {
  agents: Agent[];
  loading: boolean;
  error: string | null;
}

const initialState: AgentState = {
  agents: [],
  loading: false,
  error: null,
};

const agentSlice = createSlice({
  name: 'agents',
  initialState,
  reducers: {
    setAgents: (state, action: PayloadAction<Agent[]>) => {
      state.agents = action.payload;
    },
    addAgent: (state, action: PayloadAction<Agent>) => {
      state.agents.push(action.payload);
    },
    updateAgent: (state, action: PayloadAction<{ id: string; updates: Partial<Agent> }>) => {
      const index = state.agents.findIndex(a => a.id === action.payload.id);
      if (index !== -1) {
        state.agents[index] = { ...state.agents[index], ...action.payload.updates };
      }
    },
  },
});

export const { setAgents, addAgent, updateAgent } = agentSlice.actions;
export default agentSlice.reducer;
```

### Real-Time Communication

```typescript
// WebSocket hook for agent updates
const useAgentWebSocket = (agentId: string) => {
  const [messages, setMessages] = useState<AgentMessage[]>([]);
  const queryClient = useQueryClient();
  
  useEffect(() => {
    const ws = new WebSocket(`ws://api/ws/agents/${agentId}`);
    
    ws.onopen = () => {
      console.log('WebSocket connected');
    };
    
    ws.onmessage = (event) => {
      const message = JSON.parse(event.data);
      setMessages(prev => [...prev, message]);
      
      // Invalidate queries to refetch fresh data
      queryClient.invalidateQueries(['agent', agentId]);
    };
    
    ws.onerror = (error) => {
      console.error('WebSocket error:', error);
    };
    
    ws.onclose = () => {
      console.log('WebSocket disconnected');
    };
    
    return () => ws.close();
  }, [agentId, queryClient]);
  
  return { messages };
};
```

## 🧪 Testing Strategy

### Test-Driven Development (TDD)

1. **Write the test first** - Define expected behavior before implementation
2. **Watch it fail** - Ensure the test actually tests something
3. **Write minimal code** - Just enough to make the test pass
4. **Refactor** - Improve code while keeping tests green
5. **Repeat** - One test at a time

### Test Organization

#### Unit Tests (Vitest/Jest)
Test utility functions and hooks

```typescript
// Example: Utility function test
import { describe, it, expect } from 'vitest';
import { formatAgentStatus } from './formatters';

describe('formatAgentStatus', () => {
  it('should format active status correctly', () => {
    expect(formatAgentStatus('active')).toBe('Active');
  });
  
  it('should format paused status correctly', () => {
    expect(formatAgentStatus('paused')).toBe('Paused');
  });
});
```

#### Component Tests (@testing-library/react)
Test component rendering and interaction

```typescript
// Example: Component test
import { render, screen, fireEvent } from '@testing-library/react';
import { AgentCard } from './AgentCard';

describe('AgentCard', () => {
  const mockAgent = {
    id: '1',
    name: 'Test Agent',
    status: 'active',
  };
  
  it('displays agent name', () => {
    render(<AgentCard agent={mockAgent} />);
    expect(screen.getByText('Test Agent')).toBeInTheDocument();
  });
  
  it('calls onSelect when clicked', () => {
    const handleSelect = vi.fn();
    render(<AgentCard agent={mockAgent} onSelect={handleSelect} />);
    
    fireEvent.click(screen.getByRole('button'));
    expect(handleSelect).toHaveBeenCalledWith('1');
  });
});
```

#### Integration Tests
Test component interaction and data flow

```typescript
// Example: Integration test
import { render, screen, waitFor } from '@testing-library/react';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { AgentDashboard } from './AgentDashboard';

const createTestQueryClient = () => new QueryClient({
  defaultOptions: { queries: { retry: false } },
});

describe('AgentDashboard Integration', () => {
  it('loads and displays agents', async () => {
    const queryClient = createTestQueryClient();
    
    render(
      <QueryClientProvider client={queryClient}>
        <AgentDashboard />
      </QueryClientProvider>
    );
    
    await waitFor(() => {
      expect(screen.getByText('Test Agent')).toBeInTheDocument();
    });
  });
});
```

#### E2E Tests (Playwright/Cypress)
Browser automation for complete user flows

```typescript
// Playwright example
import { test, expect } from '@playwright/test';

test('user can create and view agent', async ({ page }) => {
  await page.goto('/agents');
  
  // Click create agent button
  await page.click('button:has-text("Create Agent")');
  
  // Fill form
  await page.fill('input[name="name"]', 'New Agent');
  await page.click('button:has-text("Submit")');
  
  // Verify agent appears in list
  await expect(page.locator('text=New Agent')).toBeVisible();
});
```

### Test Coverage Goals
- Aim for **80%+ code coverage**, but focus on critical paths
- Keep test files next to components or in `__tests__` directories

## 🛡️ Security Best Practices

### Frontend Security

- **Sanitize user inputs** - prevent XSS attacks
- **Content Security Policy (CSP)** - restrict resource loading
- **Secure API communications** - use HTTPS, include CSRF tokens
- **Token storage** - use httpOnly cookies or secure storage mechanisms
- **Dependency scanning** - regularly audit npm packages for vulnerabilities
- **Environment variables** - never expose secrets in frontend code

```typescript
// Example: Sanitize user input
import DOMPurify from 'dompurify';

const SafeContent: React.FC<{ html: string }> = ({ html }) => {
  const sanitized = DOMPurify.sanitize(html);
  return <div dangerouslySetInnerHTML={{ __html: sanitized }} />;
};

// Example: Secure token handling
const useAuth = () => {
  const getToken = () => {
    // Prefer httpOnly cookies set by backend
    // If using localStorage, be aware of XSS risks
    return localStorage.getItem('token');
  };
  
  const setToken = (token: string) => {
    // Consider using httpOnly cookies instead
    localStorage.setItem('token', token);
  };
  
  return { getToken, setToken };
};
```

## ⚡ Performance Optimization

### React Optimization

#### Prevent Unnecessary Re-renders

```typescript
// Use React.memo for components
const AgentCard = React.memo<AgentCardProps>(({ agent, onSelect }) => {
  return (
    <div onClick={() => onSelect(agent.id)}>
      {agent.name}
    </div>
  );
});

// Use useMemo for expensive computations
const AgentList: React.FC<Props> = ({ agents }) => {
  const sortedAgents = useMemo(
    () => agents.sort((a, b) => a.priority - b.priority),
    [agents]
  );
  
  return <>{sortedAgents.map(agent => <AgentCard key={agent.id} agent={agent} />)}</>;
};

// Use useCallback for event handlers
const AgentSelector: React.FC = () => {
  const [selected, setSelected] = useState<string | null>(null);
  
  const handleSelect = useCallback((id: string) => {
    setSelected(id);
  }, []); // Stable reference
  
  return <AgentList onSelect={handleSelect} />;
};
```

#### Code Splitting and Lazy Loading

```typescript
// Lazy load pages
import { lazy, Suspense } from 'react';

const AgentPage = lazy(() => import('./pages/AgentPage'));
const TasksPage = lazy(() => import('./pages/TasksPage'));

const App = () => {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      <Routes>
        <Route path="/agents" element={<AgentPage />} />
        <Route path="/tasks" element={<TasksPage />} />
      </Routes>
    </Suspense>
  );
};
```

#### Virtualization for Long Lists

```typescript
import { useVirtualizer } from '@tanstack/react-virtual';

const VirtualAgentList: React.FC<{ agents: Agent[] }> = ({ agents }) => {
  const parentRef = useRef<HTMLDivElement>(null);
  
  const virtualizer = useVirtualizer({
    count: agents.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 100, // Estimated row height
  });
  
  return (
    <div ref={parentRef} style={{ height: '600px', overflow: 'auto' }}>
      <div style={{ height: `${virtualizer.getTotalSize()}px`, position: 'relative' }}>
        {virtualizer.getVirtualItems().map(virtualItem => (
          <div
            key={virtualItem.key}
            style={{
              position: 'absolute',
              top: 0,
              left: 0,
              width: '100%',
              height: `${virtualItem.size}px`,
              transform: `translateY(${virtualItem.start}px)`,
            }}
          >
            <AgentCard agent={agents[virtualItem.index]} />
          </div>
        ))}
      </div>
    </div>
  );
};
```

### Loading Strategies

#### Skeleton Screens

```typescript
const AgentCardSkeleton: React.FC = () => {
  return (
    <div className="skeleton-card">
      <div className="skeleton-avatar" />
      <div className="skeleton-text" />
      <div className="skeleton-text short" />
    </div>
  );
};

const AgentList: React.FC = () => {
  const { agents, isLoading } = useAgents();
  
  if (isLoading) {
    return (
      <>
        {Array.from({ length: 5 }).map((_, i) => (
          <AgentCardSkeleton key={i} />
        ))}
      </>
    );
  }
  
  return <>{agents.map(agent => <AgentCard key={agent.id} agent={agent} />)}</>;
};
```

#### Optimistic Updates

```typescript
const useUpdateAgent = () => {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: updateAgent,
    onMutate: async (newData) => {
      // Cancel outgoing refetches
      await queryClient.cancelQueries(['agent', newData.id]);
      
      // Snapshot previous value
      const previous = queryClient.getQueryData(['agent', newData.id]);
      
      // Optimistically update
      queryClient.setQueryData(['agent', newData.id], newData);
      
      return { previous };
    },
    onError: (err, newData, context) => {
      // Rollback on error
      queryClient.setQueryData(['agent', newData.id], context?.previous);
    },
    onSettled: (data, error, variables) => {
      // Refetch after mutation
      queryClient.invalidateQueries(['agent', variables.id]);
    },
  });
};
```

## 🔧 Error Handling

### Error Boundaries

```typescript
import React, { Component, ErrorInfo, ReactNode } from 'react';

interface Props {
  children: ReactNode;
  fallback?: ReactNode;
}

interface State {
  hasError: boolean;
  error: Error | null;
}

export class ErrorBoundary extends Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false, error: null };
  }
  
  static getDerivedStateFromError(error: Error): State {
    return { hasError: true, error };
  }
  
  componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    console.error('Error caught by boundary:', error, errorInfo);
    // Log to error tracking service (e.g., Sentry)
  }
  
  render() {
    if (this.state.hasError) {
      return this.props.fallback || (
        <div className="error-fallback">
          <h2>Something went wrong</h2>
          <p>{this.state.error?.message}</p>
        </div>
      );
    }
    
    return this.props.children;
  }
}
```

### Query Error Handling

```typescript
const AgentDetails: React.FC<{ agentId: string }> = ({ agentId }) => {
  const { data, error, isError, refetch } = useQuery({
    queryKey: ['agent', agentId],
    queryFn: () => fetchAgent(agentId),
    retry: 3,
    onError: (error) => {
      toast.error(`Failed to load agent: ${error.message}`);
    },
  });
  
  if (isError) {
    return (
      <ErrorDisplay 
        message="Failed to load agent details"
        onRetry={refetch}
      />
    );
  }
  
  return <div>{/* Agent details */}</div>;
};
```

## 📝 Documentation Standards

### Component Documentation

```typescript
/**
 * AgentCard component displays agent information and status
 * 
 * @example
 * ```tsx
 * <AgentCard 
 *   agent={agentData} 
 *   onSelect={(id) => handleSelect(id)}
 *   className="custom-class"
 * />
 * ```
 */
interface AgentCardProps {
  /** The agent data to display */
  agent: Agent;
  /** Optional callback when agent is selected */
  onSelect?: (agentId: string) => void;
  /** Optional className for custom styling */
  className?: string;
}

export const AgentCard: React.FC<AgentCardProps> = ({ agent, onSelect, className }) => {
  // Implementation
};
```

### Storybook for Component Documentation

```typescript
// AgentCard.stories.tsx
import type { Meta, StoryObj } from '@storybook/react';
import { AgentCard } from './AgentCard';

const meta: Meta<typeof AgentCard> = {
  title: 'Components/Agent/AgentCard',
  component: AgentCard,
  tags: ['autodocs'],
  argTypes: {
    onSelect: { action: 'selected' },
  },
};

export default meta;
type Story = StoryObj<typeof AgentCard>;

export const Active: Story = {
  args: {
    agent: {
      id: '1',
      name: 'Research Agent',
      status: 'active',
    },
  },
};

export const Paused: Story = {
  args: {
    agent: {
      id: '2',
      name: 'Analysis Agent',
      status: 'paused',
    },
  },
};
```

## 🚀 Deployment

### Frontend Dockerfile

```dockerfile
FROM node:20-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Environment Variables

```typescript
// config/env.ts
interface EnvConfig {
  apiUrl: string;
  wsUrl: string;
  environment: 'development' | 'staging' | 'production';
}

export const env: EnvConfig = {
  apiUrl: import.meta.env.VITE_API_URL || 'http://localhost:8000',
  wsUrl: import.meta.env.VITE_WS_URL || 'ws://localhost:8000',
  environment: (import.meta.env.VITE_ENV as EnvConfig['environment']) || 'development',
};
```

## 📊 Monitoring

### Error Tracking (Sentry)

```typescript
import * as Sentry from '@sentry/react';

Sentry.init({
  dsn: import.meta.env.VITE_SENTRY_DSN,
  integrations: [
    new Sentry.BrowserTracing(),
    new Sentry.Replay(),
  ],
  tracesSampleRate: 1.0,
  replaysSessionSampleRate: 0.1,
  replaysOnErrorSampleRate: 1.0,
  environment: import.meta.env.VITE_ENV,
});
```

### Analytics

```typescript
// utils/analytics.ts
export const analytics = {
  track: (eventName: string, properties?: Record<string, any>) => {
    if (import.meta.env.PROD) {
      // Send to analytics service
      console.log('Track:', eventName, properties);
    }
  },
  
  page: (pageName: string) => {
    if (import.meta.env.PROD) {
      console.log('Page view:', pageName);
    }
  },
};

// Usage in components
const AgentPage = () => {
  useEffect(() => {
    analytics.page('Agent Dashboard');
  }, []);
  
  const handleAgentSelect = (id: string) => {
    analytics.track('agent_selected', { agentId: id });
  };
};
```

## 📚 Useful Resources

### React Ecosystem
- **React**: https://react.dev/
- **Next.js**: https://nextjs.org/docs
- **Vite**: https://vitejs.dev/
- **React Query**: https://tanstack.com/query/latest
- **Zustand**: https://zustand-demo.pmnd.rs/

### UI Libraries
- **shadcn/ui**: https://ui.shadcn.com/
- **Material-UI**: https://mui.com/
- **Tailwind CSS**: https://tailwindcss.com/
- **Radix UI**: https://www.radix-ui.com/

### TypeScript
- **TypeScript Handbook**: https://www.typescriptlang.org/docs/
- **Type Challenges**: https://github.com/type-challenges/type-challenges

### Testing
- **Vitest**: https://vitest.dev/
- **Testing Library**: https://testing-library.com/
- **Playwright**: https://playwright.dev/
- **Cypress**: https://www.cypress.io/

### Best Practices
- **Airbnb JavaScript Style Guide**: https://github.com/airbnb/javascript
- **Google TypeScript Style Guide**: https://google.github.io/styleguide/tsguide.html
- **React Best Practices**: https://react.dev/learn/thinking-in-react

## ⚠️ Important Notes

### Development Guidelines

- **NEVER ASSUME OR GUESS** - When in doubt, ask for clarification
- **Always verify file paths and module names** before use
- **Test your code** - No feature is complete without tests
- **Document your decisions** - Future developers (including yourself) will thank you
- **Keep it simple** - Complexity is the enemy of maintainability
- **Think in components** - Build reusable, composable UI elements
- **Accessibility first** - Ensure all users can interact with your application

### Frontend Best Practices

- **Type safety** - Use TypeScript for all components and utilities
- **Performance** - Lazy load components, memoize expensive computations
- **Accessibility** - Use semantic HTML, ARIA labels, keyboard navigation
- **Responsive design** - Build mobile-first, test on multiple devices
- **Error handling** - Use error boundaries and provide fallback UI
- **Loading states** - Show skeleton screens or spinners during data fetching
- **Optimistic updates** - Update UI immediately for better UX

### Code Review Checklist

- [ ] Code follows style guide and naming conventions
- [ ] Components are properly typed with TypeScript
- [ ] Tests are included and passing
- [ ] Accessibility requirements met (ARIA, semantic HTML, keyboard navigation)
- [ ] Error handling is comprehensive
- [ ] Performance implications considered (memoization, lazy loading)
- [ ] Responsive design tested on multiple screen sizes
- [ ] No console errors or warnings
- [ ] Dependencies are up to date and secure
