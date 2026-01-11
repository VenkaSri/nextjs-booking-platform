# Educational Services Platform

> **Full-Stack Next.js Application** - A production-ready platform for educational services with payment processing, session management, and CMS integration.


[![Stripe](https://img.shields.io/badge/Stripe-Payment-green)](https://stripe.com/)

## Overview

A production full-stack web application for an educational services company, supporting paid registrations, session scheduling, and content management. The platform enables students to register for tutoring sessions, academic competitions, and mock exams with integrated payment processing, automated email notifications, and content management.

### Key Features

- **Payment Processing** - Integrated Stripe Checkout with webhook handling
- **Session Management** - Automated session sync from CMS to database
- **Multi-Step Registration** - Complex form wizard with real-time validation
- **Email System** - React-based transactional email templates
- **CMS Integration** - Headless CMS with Sanity for content management
- **SEO Optimized** - Structured data, sitemaps, and performance optimization
- **Testing** - Comprehensive test suite with Vitest
- **Type Safety** - End-to-end TypeScript with Zod validation

## Tech Stack

### Frontend
- **Next.js 16** (App Router) - React framework with server-side rendering
- **React 19** - Latest React with modern concurrent features
- **TypeScript** - Strict type safety throughout
- **Tailwind CSS v4** - Utility-first styling
- **React Hook Form** - Performant form handling
- **Zod** - Schema validation

### Backend
- **Next.js API Routes** - Serverless API endpoints
- **Supabase (PostgreSQL)** - Database with real-time capabilities
- **Stripe** - Payment processing
- **Sanity CMS** - Headless content management
- **Resend** - Transactional email service

### Infrastructure
- **Vercel** - Deployment and hosting
- **Vitest** - Testing framework
- **MSW** - API mocking

## Project Structure

```
├── web/                    # Next.js application
│   ├── app/               # App Router pages and API routes
│   ├── components/        # React components
│   ├── lib/               # Utility functions and services
│   ├── validation/        # Zod schemas
│   └── emails/            # React Email templates
├── studio/                # Sanity CMS studio
│   └── schemaTypes/       # CMS schema definitions
```

## Architecture

- **Monorepo Structure** - Separate workspaces for web app and CMS
- **Server Components** - Leveraging Next.js App Router
- **Dependency Injection** - Testable service layer
- **Repository Pattern** - Abstracted data access
- **Type-Safe APIs** - End-to-end TypeScript

For detailed architecture documentation, see [ARCHITECTURE.md](./ARCHITECTURE.md).

## Highlights

### Payment Flow
- Stripe Checkout integration with idempotency
- Pending registration tracking with expiry
- Webhook verification and error handling
- Duplicate registration prevention

### Session Management
- Automated sync from CMS via cron jobs
- Timezone-aware scheduling
- Capacity management and enrollment tracking
- Real-time availability checking

### Developer Experience
- Comprehensive test coverage
- ESLint and code quality tools
- Clear separation of concerns

## Privacy Note

This repository is private to protect client confidentiality. The codebase contains production-ready implementations but cannot be publicly shared. For technical discussions or code samples, please reach out.

## Documentation

- **[PORTFOLIO.md](./PORTFOLIO.md)** - Detailed project highlights and accomplishments
- **[ARCHITECTURE.md](./ARCHITECTURE.md)** - System architecture and design patterns


**Note**: This is a private repository. For portfolio purposes, see [PORTFOLIO.md](./PORTFOLIO.md) for detailed accomplishments.
