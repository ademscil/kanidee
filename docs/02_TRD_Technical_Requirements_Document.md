# TRD: Technical Requirements Document
**Project Name:** Kanidee Platform (Digital Health E-Commerce & Content Ecosystem)  
**Document Identifier:** KND-TRD-002  
**Version:** 2.0.0-ENTERPRISE  
**Date:** October 2026  
**Author Role:** Senior Principal Technical Architect & Systems Engineer  
**Classification:** Confidential / Client Engineering Specification  

---

## 1. System Architecture & Infrastructure Topology

The Kanidee web application is architected as an enterprise-grade, edge-accelerated full-stack solution. The frontend utilizes **Next.js 16/15 App Router** running on **Node.js 22 LTS / 23 Hardened**, integrated with **React 19**, **TypeScript 5.9 (Strict Mode)**, and **Tailwind CSS v4**. Data persistence is managed by **Prisma ORM 7** over a high-throughput **PostgreSQL 17** database hosted on Supabase or Neon.

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                          SYSTEM ARCHITECTURE & DATA FLOW                        │
└─────────────────────────────────────────────────────────────────────────────────┘
                                         │
                             [ HTTPS / TLS 1.3 / HTTP/2 ]
                                         ▼
                             [ Cloudflare Edge WAF & CDN ]
                             • DDoS Layer 3/4/7 Mitigation
                             • Global Anycast DNS Resolution
                             • Static Asset & Image Edge Caching
                                         │
                             [ Reverse Proxy & Edge Routing ]
                                         ▼
                       [ Next.js App Router (Node.js 22 LTS) ]
              ┌──────────────────────────┴──────────────────────────┐
              ▼                                                     ▼
    [ Public Client Surfaces ]                            [ Server Core Engine ]
    • 1. Home & Bio Links (/ & /links)                    • Server Actions & API Handlers
    • 2. Product Catalog (/produk)                        • Zod 4 Runtime Payload Sanitizer
    • 2. Direct Checkout (/checkout/:slug)                • Midtrans Snap Server Integration
    • 3. Artikel Blog (/artikel)                          • Webhook Signature Verifier
    • 4. Download Portal (/download)                      • PDF Watermarking & Asset Streamer
    • 5. CMS Backoffice (/cms)                            • JWT Admin Session Guard
    • 6. Profil (/profil)                                 • Security Audit Logger
              │                                                     │
              ▼                                                     ▼
    [ Client Hydration (React 19) ]                       [ Prisma 7 Connection Pool ]
    • Zero Hydration Errors                               • PgBouncer / Supavisor Pooling
    • LightningCSS Utility Engine                         • Interactive ACID Transactions
    • Confetti Celebration Micro-Interactions                       │
    • Midtrans Snap.js Modal Script                                 ▼
                                                      [ PostgreSQL 17 ACID Database ]
                                                      • Products & Articles
                                                      • Orders & Payment Transactions
                                                      • Expiring Download Access Tokens
                                                      • Admin Users & Security Audit Logs
```

---

## 2. Standardized Highest-Version Technology Stack

To ensure complete immunity from known legacy vulnerabilities (including historical PHP RCEs and Node.js transitive dependency issues), all core packages are strictly pinned to their highest hardened production versions:

| Architectural Layer | Technology Selected | Version Spec | Engineering Rationale |
|---|---|---|---|
| **Runtime Environment** | Node.js (Active LTS) | `>= 22.14.0 LTS` / `23.x` | Modern V8 engine, native Web Crypto, hardened `llhttp` parser impervious to HTTP request smuggling. |
| **Primary Language** | TypeScript | `^5.9.3` | Enforces compile-time type invariance; strict null checking, `exactOptionalPropertyTypes`, and zero `any`. |
| **Full-Stack Framework** | Next.js (App Router) | `^16.4.3` / `15.x Hardened`| Built-in React Server Components (RSC), optimized Server Actions, streaming SSR, and automated edge routing. |
| **UI Library & Engine** | React & React DOM | `^19.3.1` | Native Server Actions support, enhanced concurrent rendering, and zero hydration mismatch artifacts. |
| **CSS Utility Framework**| Tailwind CSS | `^4.3.3` | Next-generation engine built on LightningCSS; zero runtime overhead, instant JIT compilation, and modern CSS variables. |
| **PostCSS Processor** | `@tailwindcss/postcss` | `^4.3.3` | Optimized build-time CSS bundling. |
| **Database ORM** | Prisma ORM | `^7.9.1` | Type-safe query generation, automated database migrations, and connection pooling support. |
| **Postgres Adapter** | `@prisma/adapter-pg` + `pg` | `^7.9.1` / `^8.22.0` | High-performance PostgreSQL client connection pooler. |
| **Database Server** | PostgreSQL | `17.x` | Modern ACID-compliant transactional relational database engine. |
| **Runtime Data Validator**| Zod | `^4.6.7` | Deep payload sanitization and strict schema validation for all incoming requests. |
| **Iconography** | Lucide React | `^1.28.0` | Accessible, tree-shakeable SVG icon suite. |
| **Payment Gateway** | Midtrans Snap Client & API | Production v2 | Native Indonesian payment rails (QRIS 2.0, Virtual Accounts, GoPay, ShopeePay). |
| **Authentication** | `jsonwebtoken` + `bcryptjs` | `^9.0.2` / `^3.0.3` | Secure HttpOnly cookie-based JWT sessions with salted password hashing. |
| **Email Delivery** | `resend` / `nodemailer` | `^6.18.1` / `^7.0.13` | Transactional email generation for order receipts and download recovery. |
| **Unit & Integration Test**| Vitest | `^5.0.5` | Blazing-fast Vite-native testing engine for business logic and payment signatures. |
| **End-to-End Test** | Playwright | `^1.62.1` | Automated cross-browser test harness covering the complete purchase journey. |

---

## 3. Production Configuration Manifests

### 3.1 Pinned `package.json` with Vulnerability Overrides
Modeled after enterprise standards from PT Astra Otoparts (`portal-csi-github`) and `saas-wedding`:

```json
{
  "name": "kanidee-platform",
  "version": "1.0.0",
  "private": true,
  "engines": {
    "node": ">=22.14.0",
    "npm": ">=10.9.0"
  },
  "scripts": {
    "clean": "node -e \"['.next','tsconfig.tsbuildinfo'].forEach(p=>require('fs').rmSync(p,{recursive:true,force:true}))\"",
    "predev": "npm run clean",
    "dev": "next dev",
    "prebuild": "npm run clean",
    "build": "prisma generate && next build",
    "start": "next start",
    "lint": "eslint . --max-warnings=0",
    "type-check": "tsc --noEmit",
    "test": "vitest run",
    "test:coverage": "vitest run --coverage",
    "test:e2e": "playwright test",
    "audit:security": "npm audit --audit-level=high",
    "db:migrate": "prisma migrate deploy",
    "db:generate": "prisma generate",
    "db:seed": "tsx prisma/seed.ts"
  },
  "dependencies": {
    "next": "^16.4.3",
    "react": "^19.3.1",
    "react-dom": "^19.3.1",
    "@prisma/client": "^7.9.1",
    "@prisma/adapter-pg": "^7.9.1",
    "pg": "^8.22.0",
    "zod": "^4.6.7",
    "clsx": "^2.1.1",
    "tailwind-merge": "^3.6.0",
    "lucide-react": "^1.28.0",
    "bcryptjs": "^3.0.3",
    "jsonwebtoken": "^9.0.2",
    "nodemailer": "^7.0.13",
    "resend": "^6.18.1",
    "nanoid": "^3.3.17",
    "qrcode": "^1.5.4",
    "canvas-confetti": "^1.9.4"
  },
  "devDependencies": {
    "typescript": "^5.9.3",
    "@types/node": "^22.20.5",
    "@types/react": "^19.0.0",
    "@types/react-dom": "^19.0.0",
    "@types/pg": "^8.20.3",
    "@types/qrcode": "^1.5.6",
    "@types/bcryptjs": "^2.4.6",
    "@types/jsonwebtoken": "^9.0.8",
    "@types/canvas-confetti": "^1.9.0",
    "tailwindcss": "^4.3.3",
    "@tailwindcss/postcss": "^4.3.3",
    "postcss": "^8.5.18",
    "eslint": "^9.39.5",
    "eslint-config-next": "^16.4.3",
    "prisma": "^7.9.1",
    "vitest": "^5.0.5",
    "@vitest/coverage-v8": "^4.1.12",
    "@playwright/test": "^1.62.1",
    "tsx": "^4.23.1"
  },
  "overrides": {
    "sharp": "^0.35.5",
    "postcss": "^8.5.18",
    "brace-expansion": "^2.1.7",
    "js-yaml": "^4.3.2",
    "nanoid": "^3.3.17",
    "browserslist": "^4.28.7",
    "qs": "^6.16.0",
    "ip-address": "^10.7.4",
    "undici": "^8.12.2",
    "source-map-js": "^1.2.2"
  }
}
```

### 3.2 Ultra-Strict TypeScript Compiler Configuration (`tsconfig.json`)
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowJs": false,
    "skipLibCheck": true,
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "noImplicitThis": true,
    "alwaysStrict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": [
    "next-env.d.ts",
    "**/*.ts",
    "**/*.tsx",
    ".next/types/**/*.ts"
  ],
  "exclude": [
    "node_modules"
  ]
}
```

### 3.3 Security-Hardened Next.js Configuration (`next.config.ts`)
```typescript
import type { NextConfig } from 'next';

const nextConfig: NextConfig = {
  reactStrictMode: true,
  poweredByHeader: false, // Disables 'X-Powered-By: Next.js' signature
  compress: true,
  images: {
    formats: ['image/avif', 'image/webp'],
    remotePatterns: [
      {
        protocol: 'https',
        hostname: 'images.unsplash.com',
      },
      {
        protocol: 'https',
        hostname: 'app.midtrans.com',
      },
    ],
  },
  async headers() {
    return [
      {
        source: '/:path*',
        headers: [
          {
            key: 'X-DNS-Prefetch-Control',
            value: 'on',
          },
          {
            key: 'Strict-Transport-Security',
            value: 'max-age=63072000; includeSubDomains; preload',
          },
          {
            key: 'X-Content-Type-Options',
            value: 'nosniff',
          },
          {
            key: 'X-Frame-Options',
            value: 'SAMEORIGIN',
          },
          {
            key: 'Referrer-Policy',
            value: 'origin-when-cross-origin',
          },
          {
            key: 'Permissions-Policy',
            value: 'camera=(), microphone=(), geolocation=(), browsing-topics=()',
          },
          {
            key: 'Content-Security-Policy',
            value: [
              "default-src 'self'",
              "script-src 'self' 'unsafe-inline' 'unsafe-eval' https://app.sandbox.midtrans.com https://app.midtrans.com",
              "style-src 'self' 'unsafe-inline'",
              "img-src 'self' data: blob: https://images.unsplash.com https://app.sandbox.midtrans.com https://app.midtrans.com",
              "font-src 'self' data:",
              "connect-src 'self' https://app.sandbox.midtrans.com https://app.midtrans.com https://api.sandbox.midtrans.com https://api.midtrans.com",
              "frame-src 'self' https://app.sandbox.midtrans.com https://app.midtrans.com",
            ].join('; '),
          },
        ],
      },
    ];
  },
};

export default nextConfig;
```

---

## 4. Environment Variables Specification (`src/lib/env.ts`)

Every environment variable is validated at application startup using Zod. If any required secret is missing or misformatted, the build/process fails immediately with a descriptive diagnostic message:

```typescript
import { z } from 'zod';

const envSchema = z.object({
  // Runtime & Node Environment
  NODE_ENV: z.enum(['development', 'test', 'production']).default('development'),
  NEXT_PUBLIC_APP_URL: z.string().url().default('http://localhost:3000'),

  // Database Connection
  DATABASE_URL: z.string().min(1, 'DATABASE_URL is required for Prisma connection'),

  // Midtrans Payment Gateway
  MIDTRANS_SERVER_KEY: z.string().min(1, 'MIDTRANS_SERVER_KEY is mandatory for payments'),
  NEXT_PUBLIC_MIDTRANS_CLIENT_KEY: z.string().min(1, 'NEXT_PUBLIC_MIDTRANS_CLIENT_KEY is mandatory for Snap'),
  NEXT_PUBLIC_MIDTRANS_SNAP_URL: z.string().url().default('https://app.sandbox.midtrans.com/snap/snap.js'),
  MIDTRANS_IS_PRODUCTION: z.string().transform((v) => v === 'true').default('false'),

  // Admin CMS Security
  JWT_SECRET: z.string().min(32, 'JWT_SECRET must be at least 32 characters for security'),
  ADMIN_DEFAULT_EMAIL: z.string().email().default('admin@kanidee.com'),
  ADMIN_DEFAULT_PASSWORD: z.string().min(8).default('KanideeSecure2026!'),

  // Email Notification Delivery (Optional/Resend)
  RESEND_API_KEY: z.string().optional(),
  EMAIL_FROM: z.string().default('Kanidee <halo@kanidee.com>'),
});

export const env = envSchema.parse(process.env);
```

---

## 5. Non-Functional Performance & Reliability Budgets

1. **Google Core Web Vitals Targets:**
   - **Largest Contentful Paint (LCP):** $\le 1.2\text{ seconds}$ on 4G mobile connections.
   - **First Input Delay (FID) / Interaction to Next Paint (INP):** $\le 100\text{ ms}$.
   - **Cumulative Layout Shift (CLS):** Absolute $0.00$ (enforced through explicit image aspect ratios and skeleton placeholders).
2. **Bundle Size Ceiling:**
   - First Load JS shared by all routes: $\le 85\text{ kB}$ (compressed gzip).
   - Route-specific JS chunk for `/checkout/:slug`: $\le 30\text{ kB}$.
3. **Database Concurrency & Pooling:**
   - Connection pool allocated: $10$ active connections per serverless instance via Supabase/Neon connection pooler.
   - Query execution timeout: $5,000\text{ ms}$ max; slow query log threshold: $500\text{ ms}$.
4. **Error Handling & Fault Tolerance:**
   - Gateway downtime: If Midtrans is temporarily unreachable, the checkout displays a polite error toast (*"Gateway pembayaran sedang sibuk, silakan coba 1 menit lagi atau hubungi admin via WhatsApp"*) and logs the network exception to `SecurityAuditLog`.
