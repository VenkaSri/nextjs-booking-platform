# Portfolio: Educational Services Platform

> **Note**: Developed for a client. Repository is private to protect confidentiality. This document highlights key technical achievements, architectural decisions, and delivered outcomes.

## Project Overview

A full-stack educational services platform enabling students to register for tutoring sessions, academic competitions, and mock exams. The system handles session management, payment processing, email notifications, and content management through a headless CMS.

## Tech Stack

### Frontend

- **Next.js (App Router)** – server-side rendering
- **React** – concurrent features and modern patterns
- **TypeScript** – strict type safety
- **Tailwind CSS** – utility-first styling
- **React Hook Form** – performant form handling
- **Zod** – runtime validation

### Backend & Infrastructure

- **Next.js API Routes** – serverless endpoints
- **Supabase (PostgreSQL)** – database with real-time capabilities
- **Stripe** – payment processing and checkout flow
- **Sanity CMS** – headless content management
- **Resend** – transactional email service
- **Vercel** – deployment and hosting platform

### Testing & Quality

- **Vitest** – unit and integration testing
- **Testing Library** – component testing utilities
- **MSW** – API mocking for tests
- **TypeScript Strict Mode** – compile-time safety

## Key Features & Accomplishments

### 1. Payment Processing System

- Stripe Checkout integration with webhook handling
- Idempotency for payment requests to prevent duplicates
- Pending registration system with expiry management
- Reusable checkout service built with dependency injection
- Handles race conditions, duplicate registrations, and session expiry

### 2. Multi-Step Registration Flow

- Complex form wizard with progress tracking
- Client-side and server-side validation using Zod
- Real-time session availability checking
- Responsive design for mobile and desktop
- Prevents double-booking and manages session capacity dynamically

### 3. Session Management System

- Automatic sync from CMS to database
- Manual session creation support
- Timezone-aware scheduling
- Capacity management and enrollment tracking
- Cron job integration for automated session synchronization

### 4. Email System

- React-based transactional email templates
- Booking and registration confirmation emails
- Responsive HTML emails
- Integration with Resend API
- Type-safe templates rendered from React components

### 5. CMS Integration

- Sanity CMS for content management
- Schema design for products, sessions, and blog posts
- Webhook-based incremental static regeneration (ISR)
- Image optimization with Sanity CDN

### 6. SEO & Performance

- Structured data (JSON-LD) for search visibility
- Dynamic sitemap generation
- Server-side rendering and static generation
- Image optimization with Next.js Image component
- Optimized loading for excellent Lighthouse scores

### 7. Testing Strategy

- Unit tests for business logic
- Integration tests for API routes
- Component tests with React Testing Library
- Webhook handler tests with mocked Stripe events

## Architecture Highlights

- **Dependency Injection** – Checkout service accepts dependencies for maintainable and testable code:

```typescript
export async function createCheckoutSession(
  deps: CheckoutDeps,
  input: CheckoutInput
): Promise<CheckoutResult>
```

- **Type Safety** – Strict TypeScript, runtime validation with Zod, Supabase and Sanity type generation
- **Server Components** – Next.js App Router server components reduce client JS and improve SEO
- **Error Handling** – Graceful error messages, logging, and fallback UI

## Performance & Security Highlights

- ISR pages revalidate every 60 seconds
- Code splitting and bundle analysis for optimal load times
- WebP responsive images with lazy loading
- Tailwind CSS optimized for production
- Stripe webhooks verified with signatures
- Input validation and parameterized queries
- CSRF protection and environment variable security

## Challenges Solved

- **Race conditions in payment processing** – idempotency keys and pending registration tracking
- **Timezone conversion and scheduling** for sessions
- **Multi-step form validation** with conditional logic
- **React-based email rendering** to HTML
- **Automated CMS sync** with database

## Impact & Outcomes

- Streamlined registration workflow with automated session management
- Eliminated payment-related errors via robust webhook handling
- Improved session utilization with real-time availability tracking
- Delivered responsive, SEO-optimized platform with fast page loads
- Maintained high code quality with comprehensive testing and type safety
