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
│   │   ├── payment/    # Payment UI components
│   │   │   ├── CheckoutForm/
│   │   │   ├── SubscriptionCard/
│   │   │   ├── PaymentHistory/
│   │   │   └── PricingTable/
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
│   │   ├── usePayment.ts
│   │   ├── useSubscription.ts
│   │   └── useLocalStorage.ts
│   ├── services/       # API client services
│   │   ├── api.ts
│   │   ├── agentService.ts
│   │   ├── taskService.ts
│   │   ├── paymentService.ts
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
│   │   ├── payment.ts
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

### Atomic Design Structure
- **Atoms**: Buttons, inputs, labels
- **Molecules**: Search bar, card header
- **Organisms**: Navigation, dashboards
- **Templates**: Page layouts
- **Pages**: Complete views with data

### Component Best Practices
- Use composition and custom hooks
- Extract sub-components when >200 lines
- Props with TypeScript interfaces and JSDoc
- Memoize expensive computations (useMemo)
- Stable callbacks (useCallback)
- Handle loading and error states

### State Management

**Client State (React)**
- Local: `useState` for component data
- Shared: Context API for theme, auth, preferences
- Complex: `useReducer` for state machines

**Server State (React Query - Recommended)**
- Queries: `useQuery` with caching, refetching, stale time
- Mutations: `useMutation` with optimistic updates, invalidation
- Alternative: SWR for simpler cases

**Global State (when needed)**
- **Zustand** (recommended): Lightweight, simple API
- **Redux Toolkit**: Complex apps with extensive state logic

**Real-Time (WebSocket)**
- Custom hooks for WebSocket connections
- Invalidate queries on message receipt

## 🧪 Testing Strategy

### TDD: Write test first → Watch fail → Minimal code → Refactor → Repeat

### Test Types
- **Unit (Vitest/Jest)**: Utility functions, hooks
- **Component (Testing Library)**: Rendering, interaction
- **Integration**: Component interaction, data flow
- **E2E (Playwright/Cypress)**: Complete user flows

### Coverage Goals
- **80%+** overall, focus on critical paths
- Keep tests next to components or in `__tests__/`

## 🛡️ Security Best Practices

- Sanitize user inputs (DOMPurify for HTML)
- Content Security Policy (CSP)
- Secure API: HTTPS, CSRF tokens
- Token storage: httpOnly cookies preferred over localStorage
- Dependency scanning: Audit npm packages
- Never expose secrets in frontend code

## ⚡ Performance Optimization

### React Optimization
- **React.memo**: Prevent unnecessary re-renders
- **useMemo**: Expensive computations
- **useCallback**: Stable event handlers
- **Code splitting**: Lazy load pages/components
- **Virtualization**: Long lists (@tanstack/react-virtual)

### Loading Strategies
- **Skeleton screens**: Show placeholders during load
- **Optimistic updates**: Update UI immediately, rollback on error
- **Progressive loading**: Load critical content first

## 🔧 Error Handling

### Error Boundaries
- Catch React errors in component tree
- Display fallback UI
- Log to error tracking (Sentry)

### Query Error Handling
- Retry failed queries (3 attempts)
- Display error messages (toast notifications)
- Provide retry buttons

## 📝 Documentation Standards

- **Components**: JSDoc with examples, TypeScript interfaces for props
- **Functions**: JSDoc for complex logic
- **Storybook**: Document components with interactive examples
- **README**: Setup, development, build instructions

## 🚀 Deployment

### Docker
- Multi-stage: Node 20 builder + nginx:alpine
- Build with `npm ci` and `npm run build`
- Serve static files with nginx

### Environment Variables
- `VITE_API_URL`, `VITE_WS_URL`, `VITE_STRIPE_PUBLISHABLE_KEY`
- `VITE_SENTRY_DSN`, `VITE_ENV`
- Use `import.meta.env` to access

## 💳 Payment Integration (Stripe)

### Structure
- **Types**: `types/payment.ts` - PaymentStatus, SubscriptionStatus enums, interfaces
- **Service**: `services/paymentService.ts` - API calls (create intent, subscription)
- **Hooks**: `hooks/usePayment.ts`, `hooks/useSubscription.ts` - React Query wrappers
- **Components**: `CheckoutForm`, `SubscriptionCard` - Stripe Elements integration

### Key Setup
- Load Stripe: `loadStripe(publishableKey)`
- Wrap app with `<Elements stripe={stripePromise}>`
- Use `<PaymentElement>` for card input
- Confirm payment with `stripe.confirmPayment()`

### Security
- Only use publishable keys in frontend
- Validate on backend
- Use Stripe Elements (don't build custom forms)
- Never store card data
- Test with Stripe test cards

## 📊 Monitoring

### Error Tracking (Sentry)
- Initialize with DSN, environment
- BrowserTracing, Replay integrations
- Sample rates for traces and replays

### Analytics
- Track events: `analytics.track(eventName, properties)`
- Page views: `analytics.page(pageName)`
- Only in production environment

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

### Payment Integration
- **Stripe React**: https://stripe.com/docs/stripe-js/react
- **Stripe Elements**: https://stripe.com/docs/payments/elements
- **Stripe Testing**: https://stripe.com/docs/testing

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
