# Architecture Overview

This document covers the technical decisions and structure behind this educational services booking platform. I chose a modern stack (Next.js 16, React 19, TypeScript) because it shines for this kind of e-commerce-meets-scheduling problem—server components for performance, client components where interactivity matters.

## System Layers

**Frontend Layer**
- Next.js App Router handles routing and rendering
- Server Components for all data fetching (sessions, products, CMS content)
- Client Components only where needed: forms, progress steppers, cookie consent

**Data Storage**
- Supabase (PostgreSQL) stores transactional data: sessions, bookings, pending registrations
- Sanity CMS manages editorial content: product descriptions, blog posts, homepage config

**External Integrations**
- Stripe for payments (checkout sessions, webhooks)
- Resend for transactional emails
- Vercel for hosting + cron jobs
- Cloudflare R2 for asset storage

## How Data Flows

### Registration & Payment

The booking flow is a multi-step process. When a user submits the registration form, data gets validated with Zod schemas, then passed to my checkout service. This service checks session availability, creates a pending registration (in case they abandon checkout), and generates a Stripe checkout session.

After payment, Stripe fires a webhook. My handler verifies the signature, marks the booking as confirmed, sends a confirmation email via Resend, and cleans up the pending registration record.

### Content Synchronization

Products and session templates live in Sanity (easier for non-technical team members to update). A Vercel cron job runs periodically to pull session data from Sanity, transform it, and upsert into Supabase. This keeps the relational database as the source of truth for availability while letting the CMS handle content.

When Sanity content changes, a webhook hits my `/api/webhooks/sanity` endpoint to trigger ISR revalidation—pages rebuild automatically without a full redeploy.

## API Routes

```
POST  /api/checkout           Creates Stripe checkout, returns redirect URL
GET   /api/sessions           Returns available sessions with capacity info
POST  /api/webhooks/stripe    Handles payment confirmations and failures
POST  /api/webhooks/sanity    Triggers cache revalidation on content changes
POST  /api/revalidate         Manual revalidation endpoint (admin use)
GET   /api/products           Pulls product data from Sanity
```

**Checkout Request Shape:**
```typescript
{
  sessionId: string;
  student: { name: string; grade: string; dateOfBirth: string };
  parent: { name: string; email: string; phone: string };
}
```

**Response Types:**
```typescript
{ status: "ok"; checkoutUrl: string }
{ status: "conflict"; message: string }  // duplicate registration
{ status: "full"; message: string }       // session at capacity
{ status: "not_found"; message: string }
{ status: "error"; message: string }
```

## Database Design

**sessions** – Stores date, time, timezone, capacity, current enrollment count, pricing, and links to the product it belongs to.

**bookings** – Customer info, student details, payment reference from Stripe, and the session foreign key. This is the confirmed record.

**pending_registrations** – Temporary records created at checkout start. These reserve a spot while the user completes payment. Has an expiry timestamp and Stripe session ID for correlation. A background process cleans up expired records.

## Design Decisions

### Dependency Injection for Testability

The checkout service doesn't instantiate its own database clients or Stripe SDK. Instead, it accepts them as dependencies:

```typescript
interface CheckoutDeps {
  sessions: SessionRepo;
  registrations: RegistrationsRepo;
  pending: PendingRepo;
  payments: Payments;
  config: Config;
  clock: () => number;  // injectable clock for testing timeout logic
}
```

This made unit testing straightforward—I can pass mock implementations without touching real databases.

### Repository Pattern

Data access goes through repository interfaces rather than direct Supabase calls scattered everywhere:

```typescript
interface SessionRepo {
  getById(id: string): Promise<Session | null>;
}
```

Keeps the business logic clean and database-agnostic. If I ever needed to swap Supabase for something else, changes stay contained.

### Adapter Pattern for External Services

Stripe, Resend, etc. are wrapped in adapters implementing simple interfaces:

```typescript
interface Payments {
  createCheckoutSession(payload): Promise<CheckoutSession>;
  retrieveCheckoutSession(id): Promise<CheckoutSession>;
}
```

Again, testability is the driver here. My tests use fake payment adapters that return predictable results.

### Explicit Result Types

Instead of throwing exceptions everywhere, critical operations return result objects:

```typescript
type CheckoutResult =
  | { status: "ok"; checkoutUrl: string }
  | { status: "error"; message: string }
  | { status: "conflict"; message: string };
```

The API route just pattern-matches on status and returns the appropriate HTTP code. No try/catch boilerplate obscuring the happy path.

## State Management

**Server:** React Server Components fetch data at request time (or from ISR cache). Server Actions handle form mutations where appropriate.

**Client:** React Hook Form manages form state and validation. Simple `useState` for UI toggles. URL search params store session selection so users can share links.

## Error Handling

**Client-side:** Zod validation shows inline errors. API failures surface via toast notifications. Critical errors fall back to an error boundary.

**Server-side:** API routes wrap logic in try/catch, return structured JSON errors, and log internally. The goal is graceful degradation—if CMS is down, cached content still works.

## Testing Approach

**Unit tests** cover the checkout service business logic, utility functions, and Zod schema validation.

**Integration tests** exercise API routes end-to-end with test database, including webhook signature verification.

**Component tests** focus on form behavior, error states, and accessibility.

## Deployment

Hosted on Vercel:
- Automatic deployments on push to main
- Preview deployments for PRs
- Serverless functions for API routes
- Cron jobs for the Sanity sync

**Environment Variables:**
```
STRIPE_SECRET_KEY, STRIPE_WEBHOOK_SECRET
SUPABASE_URL, SUPABASE_SERVICE_ROLE_KEY
SANITY_PROJECT_ID, SANITY_DATASET, SANITY_API_TOKEN
RESEND_API_KEY
NEXT_PUBLIC_BASE_URL
```

## Performance

- Static generation for marketing pages
- ISR (60s revalidation) for session listings
- Next.js Image component with WebP conversion
- Tailwind CSS purging keeps bundle small
- Proper PostgreSQL indexes on frequently queried columns

## Security

- Webhook signatures verified (both Stripe and Sanity)
- All user input validated with Zod before processing
- Parameterized queries only (Supabase client handles this)
- Secrets stored in environment variables, never committed
- HTTPS enforced in production

## Monitoring

- Vercel Analytics for Core Web Vitals
- Vercel logs for error tracking
- Stripe Dashboard for payment monitoring

## Future Work

Some things I'd add if this were a longer engagement:
- User authentication (probably Supabase Auth)
- Session cancellation and refund handling
- Admin dashboard for managing bookings
- Real-time updates using Supabase subscriptions
- Analytics dashboard for business metrics
