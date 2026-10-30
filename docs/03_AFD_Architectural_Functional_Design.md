# AFD: Architectural & Functional Design Document
**Project Name:** Kanidee Platform (Digital Health E-Commerce & Content Ecosystem)  
**Document Identifier:** KND-AFD-003  
**Version:** 2.0.0-ENTERPRISE  
**Date:** October 2026  
**Author Role:** Senior Principal Frontend Architect & Design Systems Lead  
**Design Standard:** Impeccable Craft UI/UX Framework (`pbakaus/impeccable`)  
**Classification:** Confidential / Client Engineering Specification  

---

## 1. Design Philosophy: Anti-AI Slop & Impeccable Craft

In strict accordance with the `pbakaus/impeccable` engineering principles, the Kanidee platform avoids generic, low-effort "AI-generated" aesthetics (such as saturated neon purple gradients, interchangeable dark-mode card grids, and cookie-cutter dashboards). Instead, Kanidee establishes an authentic, warm, scientifically grounded visual identity tailored to digital health, holistic nutrition, and personal wellness.

### 1.1 Core Aesthetic Pillars
1. **Calm Authority & Editorial Warmth:** Typography, margins, and photography evoke a premium health publication rather than a software SaaS.
2. **Tactile Ergonomics:** Controls are sized for physical fingers on mobile handsets, with immediate visual confirmation of every touch.
3. **Zero Distraction Direct Funnel:** Checkout surfaces remove extraneous navigation headers, footer clutter, and multi-field barriers, focusing 100% of the viewport on the order completion action.
4. **Accessible WCAG AAA Compliance:** High-contrast text ratios ensuring effortless reading for users across all age demographics and lighting environments.

---

## 2. Design Tokens & Visual Hierarchy

### 2.1 Color Palette Architecture (Distinct Kanidee Vitality System — Zero Herbal Green)
```
┌────────────────────────────────────────────────────────────────────────┐
│                        COLOR TOKEN PALETTE                             │
├─────────────────────┬──────────────┬───────────────────────────────────┤
│ Token Name          │ Hex Value    │ Semantic Role                     │
├─────────────────────┼──────────────┼───────────────────────────────────┤
│ `color-canvas`      │ `#F8FAFC`    │ Crisp Pearl Canvas (Body Bg)      │
│ `color-surface`     │ `#FFFFFF`    │ Pure White (Cards, Modals, Sheets)│
│ `color-text-main`   │ `#0F172A`    │ Midnight Slate (WCAG AAA 15.8:1)  │
│ `color-text-muted`  │ `#64748B`    │ Slate Steel (Captions, Meta data) │
│ `color-primary`     │ `#EA580C`    │ Sunrise Coral (Main Brand Accent) │
│ `color-primary-dark`│ `#C2410C`    │ Deep Sunset Coral (Button Hover)  │
│ `color-secondary`   │ `#0F172A`    │ Midnight Authority (CTA & Headers)│
│ `color-accent-gold` │ `#F59E0B`    │ Amber Gold (Badges, Ratings)      │
│ `color-border`      │ `#E2E8F0`    │ Fine Slate Border (Subtle Divider)│
│ `color-success`     │ `#16A34A`    │ Verified Paid & Webhook Match     │
│ `color-error`       │ `#DC2626`    │ Validation Alert Red              │
│ `color-qris`        │ `#ED1C24`    │ Official QRIS National Red        │
└─────────────────────┴──────────────┴───────────────────────────────────┘
```

### 2.2 Typography Scale & Optical Pairing
- **Display Headings:** `Plus Jakarta Sans` (weights: 600 SemiBold, 700 Bold, 800 ExtraBold). Features tight tracking (`letter-spacing: -0.02em`) and optical kerning for authoritative editorial headlines.
- **Body & Functional UI:** `Inter` (weights: 400 Regular, 500 Medium, 600 SemiBold). Optimized for reading rhythm with a generous $1.5$ line-height ratio.

$$\begin{aligned}
\text{Display 1 (Hero Title)} &: 36\text{px} \rightarrow 54\text{px} \quad (\text{Line Height } 1.15) \\
\text{Heading 1 (Page Title)} &: 28\text{px} \rightarrow 38\text{px} \quad (\text{Line Height } 1.2) \\
\text{Heading 2 (Section Title)} &: 22\text{px} \rightarrow 28\text{px} \quad (\text{Line Height } 1.3) \\
\text{Body Text (Standard)} &: 16\text{px} \quad (\text{Line Height } 1.5) \\
\text{Caption / Meta Info} &: 13\text{px} \quad (\text{Line Height } 1.4)
\end{aligned}$$

---

## 3. Responsive Layout Breakdown (Mobile to 4K Ultra-wide)

```
┌────────────────────────────────────────────────────────────────────────┐
│                        VIEWPORT MATRIX & BEHAVIOR                      │
├─────────────────────┬──────────────────────────────────────────────────┤
│ Mobile (320-480px)  │ • Single-column vertical stream                  │
│                     │ • Fixed bottom sticky checkout bar (h-16)        │
│                     │ • Fullscreen touch drawer for navigation         │
│                     │ • Instant Midtrans modal overlay                 │
├─────────────────────┼──────────────────────────────────────────────────┤
│ Tablet (768-1024px) │ • Two-column grid for product cards              │
│                     │ • Split-view checkout (Form Left / Summary Right)│
│                     │ • Visible top navigation bar with icon badges    │
├─────────────────────┼──────────────────────────────────────────────────┤
│ Desktop (1280-1920px│ • Centered content container (max-w-6xl)         │
│                     │ • Rich hero banner with lifestyle imagery        │
│                     │ • 3-column catalog grid & sticky sidebar summary │
├─────────────────────┼──────────────────────────────────────────────────┤
│ 4K UHD (2560-3840px)│ • Constrained max container width (max-w-7xl)    │
│                     │ • Proportional optical font scaling              │
│                     │ • Avoids stretching; maintains optimal line-width│
└─────────────────────┴──────────────────────────────────────────────────┘
```

---

## 4. UI Wireframes & Layout Schemas for the 6 Mandated Menus

### 4.1 Menu 1: Home (`/`) Layout Architecture
```
┌────────────────────────────────────────────────────────────────────────┐
│ [LOGO: Kanidee]   Home   Product   Artikel   Download   Profil  [WA]   │
├────────────────────────────────────────────────────────────────────────┤
│                                                                        │
│   HERO SECTION                                                         │
│   "Panduan Praktis Kesehatan Holistik & Edukasi Paripurna"             │
│   [Lihat E-Book & Program]   [Konsultasi WhatsApp Langsung]            │
│                                                                        │
├────────────────────────────────────────────────────────────────────────┤
│                                                                        │
│   QUICK BIO-LINKS (Lynk.id Replacement Bar)                            │
│   [📖 Menu Diet Sehat]   [🥗 Program Pelatihan]   [🍯 Paket Maduka]   │
│                                                                        │
├────────────────────────────────────────────────────────────────────────┤
│                                                                        │
│   FEATURED PRODUCTS (Top 3 Programs)                                   │
│   ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐     │
│   │ Menu Diet Sehat  │  │ Program Pelatihan│  │ Maduka Herbal    │     │
│   │ Rp 99.000        │  │ Rp 199.000       │  │ Rp 249.000       │     │
│   │ [Beli Sekarang]  │  │ [Beli Sekarang]  │  │ [Beli Sekarang]  │     │
│   └──────────────────┘  └──────────────────┘  └──────────────────┘     │
│                                                                        │
├────────────────────────────────────────────────────────────────────────┤
│                                                                        │
│   LATEST NUTRITION ARTICLES                                            │
│   ┌───────────────────────────┐  ┌───────────────────────────┐         │
│   │ 5 Menu Sarapan Penurun BB │  │ Fakta Gula & Diabetes     │         │
│   │ Baca 4 Menit              │  │ Baca 3 Menit              │         │
│   └───────────────────────────┘  └───────────────────────────┘         │
│                                                                        │
├────────────────────────────────────────────────────────────────────────┤
│ [FOOTER: Legal, Privacy Policy, Terms, Copyright 2026 Kanidee]         │
└────────────────────────────────────────────────────────────────────────┘
```

### 4.2 Menu 2: Direct Checkout (`/checkout/[slug]`) Wireframe
```
┌────────────────────────────────────────────────────────────────────────┐
│  ← Kembali ke Katalog                      🔒 Pembayaran Aman SSL 256  │
├───────────────────────────────────┬────────────────────────────────────┤
│ DATA PEMBELI                      │ RINGKASAN PESANAN                  │
│                                   │                                    │
│ Nama Lengkap                      │ [Thumbnail] Menu Diet Sehat        │
│ [ Adam Kurniawan                ] │ Rp 99.000                          │
│                                   │                                    │
│ Nomor WhatsApp Aktif              │ Diskon Voucher                     │
│ [ 081234567890                  ] │ - Rp 0                             │
│ (Untuk pengiriman file e-book)    │ ─────────────────────────────────  │
│                                   │ TOTAL PEMBAYARAN                   │
│ Alamat Email                      │ Rp 99.000                          │
│ [ adam@example.com              ] │                                    │
│                                   │ Kode Promo / Voucher               │
│ Catatan Khusus (Opsional)         │ [ KANIDEE10 ] [ Gunakan ]          │
│ [                               ] │                                    │
│                                   │ [ 🔒 Bayar Sekarang • Rp 99.000 ]  │
│                                   │ • Bebas biaya admin via QRIS       │
│                                   │ • Akses download otomatis terbuka  │
└───────────────────────────────────┴────────────────────────────────────┘
```

### 4.3 Menu 3: Menu Artikel (`/artikel/[slug]`) Layout
```
┌────────────────────────────────────────────────────────────────────────┐
│ [Header: Kanidee]                                                      │
├────────────────────────────────────────────────────────────────────────┤
│ Kategori: Pola Makan & Diet  •  29 Oktober 2026  •  Waktu Baca: 4 Menit │
│                                                                        │
│ # Rahasia Mengatur Porsi Makan Paripurna Tanpa Rasa Lapar              │
│                                                                        │
│ [ Foto Sampul Artikel Kualitas Tinggi ]                                │
│                                                                        │
│ Paragraf isi konten artikel dengan tipografi editorial yang nyaman...  │
│                                                                        │
│ ┌────────────────────────────────────────────────────────────────────┐ │
│ │ 💡 IN-ARTICLE CONVERSION BANNER                                    │ │
│ │ Ingin resep siap saji selama 30 hari penuh?                        │ │
│ │ [ Dapatkan E-Book Menu Sehat Sekarang (Rp 99.000) ]               │ │
│ └────────────────────────────────────────────────────────────────────┘ │
│                                                                        │
│ Lanjutan edukasi nutrisi dan tips praktis...                           │
│                                                                        │
│ [Bagikan via WhatsApp] [Bagikan via Telegram] [Salin Tautan]           │
└────────────────────────────────────────────────────────────────────────┘
```

### 4.4 Menu 4: Menu Download (`/download`) Wireframe
```
┌────────────────────────────────────────────────────────────────────────┐
│                       PORTAL PENGAMBILAN E-BOOK                        │
│                                                                        │
│ Masukkan detail pesanan Anda untuk mengunduh file digital:             │
│                                                                        │
│ Nomor Pesanan (Contoh: KND-20261029-4821)                              │
│ [ KND-20261029-4821                                                  ] │
│                                                                        │
│ Nomor WhatsApp Terdaftar                                               │
│ [ 081234567890                                                       ] │
│                                                                        │
│ [ Cari & Buka Berkas E-Book ]                                          │
│                                                                        │
│ ────────────────────────────────────────────────────────────────────── │
│ HASIL VERIFIKASI PESANAN:                                              │
│ Status: LUNAS (PAID)  •  Masa Berlaku Download: 71 Jam Tersisa         │
│                                                                        │
│ • E-Book Panduan Menu Diet Sehat (PDF) [ Unduh File (14.2 MB) ]        │
│ • Bonus Lembar Pantau Harian (PDF)     [ Unduh File (2.1 MB)  ]        │
│                                                                        │
│ Catatan: File telah diberi watermark lisensi resmi atas nama Anda.     │
└────────────────────────────────────────────────────────────────────────┘
```

### 4.5 Menu 5: CMS Admin Dashboard (`/cms/dashboard`)
```
┌──────────────┬─────────────────────────────────────────────────────────┐
│ KANIDEE CMS  │ DASHBOARD UTAMA                          [Admin Adam ▼] │
├──────────────┼─────────────────────────────────────────────────────────┤
│ • Dashboard  │ RINGKASAN PERFORMA BISNIS HARI INI                      │
│ • Produk     │ ┌──────────────┐ ┌──────────────┐ ┌───────────────────┐ │
│ • Artikel    │ │ Omzet Hari Ini│ │ Pesanan Sukses│ │ Pengunjung Hari Ini│ │
│ • Pesanan    │ │ Rp 2.450.000 │ │ 18 Transaksi │ │ 420 Sesi Pengguna │ │
│ • Voucher    │ └──────────────┘ └──────────────┘ └───────────────────┘ │
│ • Profil     │                                                         │
│ • Keluar     │ GRAFIK PENJUALAN 30 HARI TERAKHIR                       │
│              │ [ Grafik Garis Pertumbuhan Omzet Transaksi ]            │
│              │                                                         │
│              │ TRANSAKSI TERAKHIR (REAL-TIME)                          │
│              │ Order #   | Pelanggan | Produk     | Total   | Status   │
│              │ KND-4821  | Budi S.   | Menu Sehat | 199.000 | [LUNAS]  │
│              │ KND-4820  | Rina M.   | Menu Diet  |  99.000 | [LUNAS]  │
└──────────────┴─────────────────────────────────────────────────────────┘
```

---

## 5. Component Interfaces & TypeScript Props

### 5.1 Product Card Component (`ProductCard.tsx`)
```typescript
export interface ProductCardProps {
  id: string;
  slug: string;
  title: string;
  headline?: string | null;
  price: number;
  comparePrice?: number | null;
  thumbnailUrl?: string | null;
  features: string[];
  productType: 'DIGITAL_EBOOK' | 'DIGITAL_COURSE' | 'PHYSICAL_HERBAL';
}
```

### 5.2 Direct Checkout Form State (`CheckoutForm.tsx`)
```typescript
export interface CheckoutFormState {
  productSlug: string;
  customerName: string;
  customerPhone: string;
  customerEmail: string;
  notes?: string;
  voucherCode?: string;
  appliedDiscount: number;
  isSubmitting: boolean;
  errorMessage?: string | null;
}
```

---

## 6. Micro-Interactions & State Machine Behavior

1. **Voucher Validation State:**
   - Input typing: Debounced 400ms check against `/api/voucher/verify`.
   - Valid: Displays green badge (*"Diskon Rp 20.000 diterapkan!"*) with animated price counter decrement.
   - Invalid: Displays polite inline error message without clearing customer inputs.
2. **Payment Modal Execution:**
   - Button transitions into a pulsating spinner state (*"Membuka Gerbang Pembayaran..."*).
   - Upon Midtrans completion callback (`onSuccess`), triggers a multi-burst confetti celebration (`canvas-confetti`) and displays an instant download button.
3. **Zero Cumulative Layout Shift (CLS):**
   - All image elements explicitly specify `width`, `height`, and `sizes` attributes with low-quality image placeholder (LQIP) blur-up effects.
