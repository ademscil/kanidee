# Cost Optimization, Free Infrastructure & Domain Acquisition Strategy
**Project Name:** Kanidee Platform (Digital Health E-Commerce & Content Ecosystem)  
**Document Identifier:** KND-INFRA-007  
**Version:** 2.0.0-ENTERPRISE  
**Date:** October 2026  
**Author Role:** Senior Principal Cloud Architect & Financial Systems Analyst  
**Classification:** Financial & Infrastructure Planning Deliverable  

---

## 1. Executive Summary: Zero-Capex Strategy

To maximize profitability and eliminate unnecessary financial overhead for both the development team and the client, the Kanidee platform is architected entirely on **100% permissive open-source software (MIT / Apache-2.0)** and **generous, zero-cost production cloud tiers**.

Under this blueprint:
- **Ongoing Infrastructure Costs:** **Rp 0 / month** ($0).
- **Setup / Licensing Fees:** **Rp 0**.
- **The Only Necessary Expense:** Annual custom domain registration (approx. **Rp 150.000 / year** for `.com`).

---

## 2. Cloud Hosting Infrastructure Evaluation (Rp 0 / month)

### 2.1 Web Application & Edge API Hosting (Next.js 16/15 Serverless)

| Evaluation Parameter | Vercel (Hobby Tier) *(Primary)* | Netlify (Starter Tier) *(Fallback)* | Cloudflare Pages *(Alternate)* |
|---|---|---|---|
| **Monthly Subscription** | **Rp 0 ($0 / month)** | **Rp 0 ($0 / month)** | **Rp 0 ($0 / month)** |
| **Next.js Integration** | Native First-Class (Built by Next creators) | High (Next.js Runtime Plugin v5) | Good (Edge Worker adapter) |
| **Bandwidth Quota** | **100 GB / month** | **100 GB / month** | **Unlimited** |
| **Serverless Executions**| 1,000,000 requests / month | 125,000 function calls / month | 100,000 requests / day |
| **Build Pipeline** | 6,000 build minutes / month | 300 build minutes / month | 500 builds / month |
| **Edge Network & CDN** | Global Anycast CDN in 300+ cities | Global Edge Network | Global Cloudflare Network |
| **Automatic SSL/TLS** | Included (Automated Let's Encrypt) | Included (Automated SSL) | Included (Universal SSL) |
| **Custom Domain Support**| Yes (Unlimited custom domains) | Yes (Custom domain support) | Yes |
| **Deployment Mechanism** | Automated Git Push (GitHub) | Automated Git Push (GitHub) | Automated Git Push |
| **Engineering Verdict** | **RECOMMENDED PRIMARY TARGET** | **EXCELLENT FALLBACK** | **VIABLE ALTERNATE** |

### 2.2 Why Vercel Hobby Tier is Optimal for Kanidee
1. **Zero Configuration for Next.js 16/15:** Server Components, Server Actions, streaming SSR, and image optimization work out of the box without complex adapter scripts.
2. **Bandwidth Capacity:** 100 GB per month allows approximately **250,000 to 400,000 page views per month** before reaching any tier threshold.
3. **Instant Preview Environments:** Every GitHub branch generates an isolated preview URL, enabling the client to review new articles and products before merging to production.

---

## 3. Database Cloud Tier Evaluation (Free-Tier PostgreSQL 17)

Both recommended options provide managed PostgreSQL with zero credit card required upfront and 100% compatibility with **Prisma ORM 7**:

| Database Parameter | Supabase Free Tier *(Recommended)* | Neon Serverless PostgreSQL *(Fallback)* |
|---|---|---|
| **Monthly Cost** | **Rp 0 ($0 / month)** | **Rp 0 ($0 / month)** |
| **PostgreSQL Version** | PostgreSQL 16/17 Compatible | PostgreSQL 16/17 Compatible |
| **Storage Capacity** | **500 MB Dedicated Storage** | **0.5 GB Storage** |
| **Compute / Concurrency** | Dedicated Micro-instance with Pooler | Autoscaling (Scales to zero when idle) |
| **Connection Pooling** | Integrated Supavisor / PgBouncer | Integrated Serverless Pooler |
| **Active Users** | Up to 50,000 Monthly Active Users | Uncapped serverless usage |
| **Daily Automated Backups**| Yes (Stored for 7 days) | Yes (Point-in-time branch recovery) |
| **Visual Table Editor** | Yes (Built-in Web Dashboard) | Yes (Neon Console) |
| **Engineering Verdict** | **RECOMMENDED FOR KANIDEE** | **EXCELLENT DEVELOPER EXPERIENCE** |

### 3.3 Storage Capacity Calculations
$$\begin{aligned}
\text{Order Record Size} &\approx 1.5\text{ KB} \\
\text{Article Record Size} &\approx 8.0\text{ KB} \\
\text{Product Record Size} &\approx 4.0\text{ KB} \\
\text{Total Free Quota} &= 500\text{ MB} = 512,000\text{ KB}
\end{aligned}$$

A 500 MB quota accommodates **over 300,000 completed customer transactions** and hundreds of articles before the database requires any paid expansion.

---

## 4. Open-Source Component Inventory (100% Free & MIT Licensed)

Every software package utilized in the application is governed by permissive open-source licenses, incurring zero licensing fees:

| Category | Component / Library | Open Source License | Commercial Cost Replaced |
|---|---|---|---|
| **Web Framework** | Next.js 16/15 + React 19 | MIT License | Shopify / Lynk.id Storefront SaaS ($300+/yr) |
| **Styling Engine** | Tailwind CSS v4 + LightningCSS | MIT License | Premium UI Templates ($50 - $150) |
| **Database ORM** | Prisma ORM 7 + `@prisma/adapter-pg` | Apache-2.0 License | Proprietary Data Adapters (Rp 0) |
| **Icons & Visuals** | Lucide React | ISC License | Iconify / FontAwesome Pro ($99/yr) |
| **Validation Engine** | Zod 4 | MIT License | Custom Validation SaaS (Rp 0) |
| **Admin Auth** | `jsonwebtoken` + `bcryptjs` | MIT License | Clerk / Auth0 SaaS ($25 - $99/mo) |
| **Interactive Confetti**| Canvas Confetti | ISC License | Micro-interaction libraries (Rp 0) |
| **Transactional Email** | Resend (Free 3,000/mo) / Nodemailer | MIT License | Mailchimp / SendGrid ($15+/mo) |
| **Analytics Engine** | Cloudflare Web Analytics | Free & Cookie-less | Google Analytics 360 / Hotjar ($39/mo) |

---

## 5. Domain Acquisition & Registration Plan

### 5.1 Extension Options & Annual Cost Estimates
To establish professional brand authority, the client should acquire a custom Top-Level Domain (TLD):

| Domain Extension | Example Domain | Estimated Annual Cost | Recommended Registrar | Strategic Value |
|---|---|---|---|---|
| **`.com`** *(Recommended)* | `kanidee.com` | **± Rp 150.000 – Rp 175.000 / year** ($10.44/yr) | **Cloudflare Registrar** *(At cost, zero markup)* or Namecheap | Highest international credibility, universally remembered by buyers. |
| **`.id`** | `kanidee.id` | **± Rp 200.000 – Rp 250.000 / year** | Niagahoster, DomaiNesia, IDCloudHost | Official Indonesian identity; strong local consumer trust for health. |
| **`.my.id`** *(Ultra-Budget)* | `kanidee.my.id` | **± Rp 15.000 – Rp 25.000 / year** | Niagahoster, DomaiNesia, Rumahweb | Designed specifically for Indonesian creators and micro-businesses. |

### 5.2 Recommended DNS Routing Topology via Cloudflare Free
```
[ Step 1: Purchase Domain (e.g. kanidee.com) via Cloudflare or Namecheap ]
                                │
                                ▼
[ Step 2: Route Nameservers to Cloudflare Free Plan ]
  • Nameserver 1: *.ns.cloudflare.com
  • Nameserver 2: *.ns.cloudflare.com
                                │
                                ▼
[ Step 3: Configure DNS Records in Cloudflare pointing to Vercel ]
  • CNAME Record: @     -> cname.vercel-dns.com (Proxied / Orange Cloud)
  • CNAME Record: www   -> cname.vercel-dns.com (Proxied / Orange Cloud)
                                │
                                ▼
[ Step 4: Vercel Issues Universal SSL Certificate Automatically ]
                                │
                                ▼
[ Kanidee is Live at https://kanidee.com with Free SSL, WAF & Global CDN ]
```

---

## 6. Three-Year Total Cost of Ownership (TCO): Lynk.id vs. Kanidee

Assuming an average monthly transaction volume of **100 e-book sales per month** at **Rp 99.000** each (Gross Revenue: Rp 9.900.000 / month):

```
┌─────────────────────────────────┬─────────────────────┬─────────────────────┐
│ Cost Component (3-Year Period)  │ Lynk.id Platform    │ Kanidee Independent │
├─────────────────────────────────┼─────────────────────┼─────────────────────┤
│ Platform Subscription           │ Rp 3.600.000        │ Rp 0                │
│ Platform Commission Tolls       │ Rp 10.692.000 (3%)  │ Rp 0                │
│ Payment Gateway Fee (QRIS 0.7%) │ Rp 2.494.800        │ Rp 2.494.800        │
│ Cloud Hosting (Vercel)          │ Rp 0 (Included)     │ Rp 0 (Hobby Free)   │
│ Database Hosting (Supabase)     │ Rp 0 (Included)     │ Rp 0 (Free Tier)    │
│ Custom Domain Name (3 Years)    │ Rp 450.000          │ Rp 450.000          │
├─────────────────────────────────┼─────────────────────┼─────────────────────┤
│ TOTAL 3-YEAR EXPENDITURE        │ Rp 17.236.800       │ Rp 2.944.800        │
│ NET CLIENT SAVINGS              │ BASELINE            │ SAVES Rp 14.292.000 │
└─────────────────────────────────┴─────────────────────┴─────────────────────┘
```

By owning an independent platform on free-tier infrastructure, the client saves **over Rp 14.000.000 in fees across 3 years** while possessing full asset, customer data, and brand sovereignty.
