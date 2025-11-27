# Render + Vercel + Cloudflare Deployment Guide

**Purpose:** Deploy modern web apps with backend on Render, frontend on Vercel, routed through Cloudflare for security and performance.

## Prerequisites
- Domain name (e.g., `example.com`)
- Git repositories (backend/frontend)
- Accounts: Render, Vercel, Cloudflare
- Database access (Supabase, MongoDB, etc.)
- CI/CD setup (GitHub Actions recommended)

## Architecture
```
Client → Cloudflare (DNS, CDN, WAF, TLS) → {
  www.example.com → Vercel (frontend)
  api.example.com → Render (backend)
} → Databases & Third-party APIs
```

## 1. Cloudflare DNS Setup
1. Add domain to Cloudflare, update nameservers at registrar
2. Create DNS records (don't proxy yet):
   - `CNAME www` → Vercel target
   - `CNAME api` → Render domain
3. Wait for propagation

## 2. Deploy Backend (Render)

### Service Configuration
1. New Web Service → Connect Git repo (`main` branch)
2. Choose deployment method:
   - **Docker:** Provide Dockerfile
   - **Standard:** Build cmd (e.g., `pip install -r requirements.txt`), Start cmd (e.g., `gunicorn app.main:app --workers 4 --bind 0.0.0.0:$PORT`)
3. Add health check endpoint (`/healthz`)
4. Enable auto-deploy from `main`

### Environment Variables
Add secrets in Render dashboard (never commit):
```
DATABASE_URL, MONGODB_URI, STRIPE_SECRET_KEY, 
SUPABASE_SERVICE_ROLE_KEY, JWT_SECRET
```

### Security
- Use TLS for all connections
- Verify Stripe webhook signatures
- Restrict admin endpoints
- Use environment-specific secrets for staging

### Background Jobs
- Run workers as separate Render Background Workers
- Use Render Cron or external scheduler for tasks

## 3. Deploy Frontend (Vercel)

### Project Setup
1. Import Git repo to Vercel
2. Configure build settings (e.g., `npm run build`, output: `build/`)
3. Set environment variables:
   - `NEXT_PUBLIC_API_BASE=https://api.example.com` (public vars only)
4. Add custom domain: `www.example.com`

### Security Headers
Configure in `next.config.js` or Cloudflare:
```
Strict-Transport-Security, Content-Security-Policy,
X-Frame-Options: DENY, X-Content-Type-Options: nosniff
```

## 4. Cloudflare Integration

### Enable Proxy
1. Enable orange-cloud proxy for `www` and `api` DNS records
2. Set SSL/TLS to **Full (strict)**

### Security Configuration
- **WAF:** Enable managed rulesets (OWASP, common threats)
- **Rate Limiting:** 20 req/min per IP for `/api/*`
- **Bot Protection:** Enable Bot Fight Mode
- **Firewall Rules:**
  ```
  Block: (http.user_agent contains "curl") or (http.user_agent contains "python-requests")
  Rate limit: (http.request.uri.path starts_with "/api/")
  ```

### Caching
- Cache static assets aggressively (frontend)
- Bypass cache for `api.example.com/*`

## 5. Origin Protection

Prevent direct access to Render/Vercel:

**Option A: Origin Certificates** (Recommended)
- Install Cloudflare Origin Certificate on origin
- Enforce Full (strict) TLS

**Option B: Secret Header**
- Add header in Cloudflare Worker: `X-Origin-Secret: <token>`
- Validate header at origin, reject if missing

**Option C: Cloudflare Tunnel**
- Use Argo Tunnel to make origin non-publicly routable

## 6. Monitoring & Logs
- Enable logs in Render/Vercel
- Set up error tracking (Sentry)
- Configure Cloudflare analytics alerts
- Ship logs to aggregator (optional: Datadog, Papertrail)

## 7. CI/CD & Testing
- Auto-deploy `main` branch on Render/Vercel
- PR previews for frontend (Vercel)
- Staging environment for backend
- Run unit/integration tests on PRs
- E2E tests (Playwright/Cypress) on staging

## Code Snippets

### FastAPI HTTPS Redirect
```python
from starlette.responses import RedirectResponse
from fastapi import Request

@app.middleware("http")
async def enforce_https(request: Request, call_next):
    if request.url.scheme != "https" and "localhost" not in request.url.hostname:
        return RedirectResponse(request.url.replace(scheme="https"))
    return await call_next(request)
```

### Stripe Webhook Verification
```python
import stripe, os
from fastapi import Request, HTTPException

async def stripe_webhook(request: Request):
    payload = await request.body()
    sig = request.headers.get("stripe-signature")
    try:
        event = stripe.Webhook.construct_event(
            payload, sig, os.getenv("STRIPE_WEBHOOK_SECRET")
        )
    except stripe.error.SignatureVerificationError:
        raise HTTPException(400, "Invalid signature")
    # Handle event
```

## Pre-Launch Checklist
- [ ] Cloudflare nameservers configured
- [ ] DNS records (`www`, `api`) pointing correctly
- [ ] Orange-cloud proxy enabled
- [ ] TLS mode: Full (strict)
- [ ] WAF + rate limiting active
- [ ] Origin protection configured
- [ ] Webhook signatures verified
- [ ] Database TLS + access control
- [ ] Secrets in Render/Vercel (not code)
- [ ] Monitoring/logs configured
- [ ] DB backups enabled

## Rollback Plan
| Issue | Action |
|-------|--------|
| Frontend broken | Revert Vercel deployment or point DNS to maintenance page |
| Backend broken | Stop Render service, rollback Git commit |
| Exposed secrets | Rotate all keys immediately, update env vars |
| DDoS attack | Enable stricter Cloudflare challenges, increase blocking |

---

**Naming Conventions:**
- Frontend: `myapp-web`, Backend: `myapp-api`
- Branches: `main` (prod), `develop` (staging)
- Env vars: `ENV_`, `DB_`, `STRIPE_`, `SUPABASE_`, `JWT_SECRET`

