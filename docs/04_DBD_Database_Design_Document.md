# DBD: Database Design Document
**Project Name:** Kanidee Platform (Digital Health E-Commerce & Content Ecosystem)  
**Document Identifier:** KND-DBD-004  
**Version:** 2.0.0-ENTERPRISE  
**Date:** October 2026  
**Author Role:** Senior Principal Database Architect & Data Integrity Lead  
**Database Engine:** PostgreSQL 17  
**ORM Framework:** Prisma ORM 7 (`@prisma/client` + `@prisma/adapter-pg`)  
**Classification:** Confidential / Client Engineering Specification  

---

## 1. Relational Architecture & Entity Overview

The Kanidee relational database is architected to guarantee financial ACID compliance, eliminate rounding anomalies by storing monetary values as exact integers (IDR cents/rupiah), and maintain an immutable security ledger for all administrative and payment actions.

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                           ENTITY RELATIONSHIP DIAGRAM                           │
└─────────────────────────────────────────────────────────────────────────────────┘

   ┌───────────────┐           ┌───────────────┐           ┌───────────────┐
   │    Product    │───(1:N)──<│   OrderItem   │>──(N:1)───│     Order     │
   └───────────────┘           └───────────────┘           └───────────────┘
           │                                                       │
           │                                                       ├───(1:N)──< ┌────────────────────┐
           │                                                       │            │ PaymentTransaction │
           │                                                       │            └────────────────────┘
           │                                                       │
           └───────────────(1:N)──< ┌────────────────┐ >──(N:1)────┘
                                    │ DownloadAccess │
                                    └────────────────┘

   ┌───────────────┐           ┌───────────────┐           ┌────────────────────┐
   │    Article    │           │    Voucher    │           │  SecurityAuditLog  │
   └───────────────┘           └───────────────┘           └────────────────────┘

   ┌───────────────┐           ┌───────────────┐
   │   AdminUser   │           │  SiteProfile  │
   └───────────────┘           └───────────────┘
```

---

## 2. Comprehensive Data Dictionary

### 2.1 Table: `Product` (Catalog & Offerings)
Stores digital e-books, training curricula, and herbal packages.
| Column | Type | Constraints | Description |
|---|---|---|---|
| `id` | `VARCHAR(30)` | PRIMARY KEY, CUID | Unique product identifier |
| `slug` | `VARCHAR(100)` | UNIQUE, NOT NULL | URL-safe slug (e.g. `menu-sehat`, `maduka`) |
| `title` | `VARCHAR(255)` | NOT NULL | Commercial product name |
| `headline` | `VARCHAR(500)` | NULLABLE | One-line value proposition summary |
| `description`| `TEXT` | NOT NULL | Detailed sales copy and curriculum |
| `price` | `INTEGER` | NOT NULL | Selling price in Indonesian Rupiah (IDR) |
| `comparePrice`| `INTEGER` | NULLABLE | Original strikethrough price (showing discount) |
| `productType`| `ENUM` | NOT NULL, DEFAULT `DIGITAL_EBOOK` | `DIGITAL_EBOOK`, `DIGITAL_COURSE`, `PHYSICAL_HERBAL` |
| `assetUrl` | `VARCHAR(500)` | NULLABLE | Secure internal path to protected PDF guide |
| `thumbnailUrl`|`VARCHAR(500)`| NULLABLE | CDN URL for product cover card |
| `features` | `TEXT[]` | NOT NULL, DEFAULT `{}` | Array of feature bullet points |
| `isActive` | `BOOLEAN` | NOT NULL, DEFAULT `true` | Visibility toggle in public catalog |
| `sortOrder` | `INTEGER` | NOT NULL, DEFAULT `0` | Display priority on Home and Catalog |
| `createdAt` | `TIMESTAMPTZ` | NOT NULL, DEFAULT `NOW()` | Record creation timestamp |
| `updatedAt` | `TIMESTAMPTZ` | NOT NULL | Last update timestamp |

### 2.2 Table: `Order` (Sales Transactions)
Tracks customer purchases and checkout lifecycle.
| Column | Type | Constraints | Description |
|---|---|---|---|
| `id` | `VARCHAR(30)` | PRIMARY KEY, CUID | Internal unique ID |
| `orderNumber` | `VARCHAR(50)` | UNIQUE, NOT NULL | Public invoice reference (e.g. `KND-20261029-4821`) |
| `customerName`| `VARCHAR(255)`| NOT NULL | Buyer's full name (used for PDF watermark) |
| `customerEmail`|`VARCHAR(255)`| NOT NULL | Buyer's email for invoice delivery |
| `customerPhone`|`VARCHAR(50)` | NOT NULL | WhatsApp phone number (+628...) |
| `subtotal` | `INTEGER` | NOT NULL | Raw order total before vouchers |
| `discountAmount`|`INTEGER` | NOT NULL, DEFAULT `0` | Deducted discount amount |
| `totalAmount` | `INTEGER` | NOT NULL | Final payable gross amount in IDR |
| `status` | `ENUM` | NOT NULL, DEFAULT `PENDING` | `PENDING`, `PROCESSING`, `PAID`, `CANCELLED`, `EXPIRED` |
| `voucherCode` | `VARCHAR(50)` | NULLABLE | Applied promotion code |
| `notes` | `TEXT` | NULLABLE | Special buyer instructions |
| `createdAt` | `TIMESTAMPTZ` | NOT NULL, DEFAULT `NOW()` | Order creation time |
| `updatedAt` | `TIMESTAMPTZ` | NOT NULL | State change timestamp |

### 2.3 Table: `PaymentTransaction` (Gateway Ledgers)
Reconciles asynchronous callbacks from Midtrans.
| Column | Type | Constraints | Description |
|---|---|---|---|
| `id` | `VARCHAR(30)` | PRIMARY KEY, CUID | Unique transaction entry ID |
| `orderId` | `VARCHAR(30)` | FOREIGN KEY (`Order.id`), CASCADE | Parent order relation |
| `gatewayProvider`|`VARCHAR(50)`| NOT NULL, DEFAULT `'MIDTRANS'`| Gateway partner identifier |
| `gatewayReferenceId`|`VARCHAR(100)`| UNIQUE, NOT NULL | Midtrans `order_id` parameter |
| `paymentType` | `VARCHAR(50)` | NULLABLE | `qris`, `bank_transfer`, `gopay`, `shopeepay` |
| `grossAmount` | `INTEGER` | NOT NULL | Reconciled payment amount |
| `transactionStatus`|`VARCHAR(50)`| NOT NULL | Gateway status: `settlement`, `pending`, `expire` |
| `signatureVerified`|`BOOLEAN` | NOT NULL, DEFAULT `false` | True if SHA-512 signature matched |
| `rawPayload` | `JSONB` | NULLABLE | Full webhook JSON audit payload |
| `paidAt` | `TIMESTAMPTZ` | NULLABLE | Exact gateway settlement timestamp |
| `createdAt` | `TIMESTAMPTZ` | NOT NULL, DEFAULT `NOW()` | Log creation time |

### 2.4 Table: `DownloadAccess` (Digital Fulfillment & Anti-Piracy)
Controls expiring access to purchased files.
| Column | Type | Constraints | Description |
|---|---|---|---|
| `id` | `VARCHAR(30)` | PRIMARY KEY, CUID | Token record identifier |
| `orderId` | `VARCHAR(30)` | FOREIGN KEY (`Order.id`), CASCADE | Associated order relation |
| `productId` | `VARCHAR(30)` | FOREIGN KEY (`Product.id`)| Associated product relation |
| `token` | `UUID` | UNIQUE, NOT NULL, DEFAULT `UUID()`| Cryptographic access string |
| `downloadCount`|`INTEGER` | NOT NULL, DEFAULT `0` | Number of successful file streams |
| `maxDownloads` |`INTEGER` | NOT NULL, DEFAULT `5` | Anti-abuse download limit |
| `expiresAt` | `TIMESTAMPTZ` | NOT NULL | Valid until (72 hours from payment) |
| `createdAt` | `TIMESTAMPTZ` | NOT NULL, DEFAULT `NOW()` | Issuance time |

### 2.5 Table: `Article` (Menu Artikel Blog & SEO Content)
Powers the health education blog.
| Column | Type | Constraints | Description |
|---|---|---|---|
| `id` | `VARCHAR(30)` | PRIMARY KEY, CUID | Article record ID |
| `slug` | `VARCHAR(150)`| UNIQUE, NOT NULL | SEO URL slug |
| `title` | `VARCHAR(255)`| NOT NULL | Editorial headline |
| `excerpt` | `TEXT` | NOT NULL | Summary preview for cards & meta |
| `content` | `TEXT` | NOT NULL | Full Markdown or HTML article body |
| `coverImageUrl`|`VARCHAR(500)`| NULLABLE | Header banner photography |
| `category` | `VARCHAR(100)`| NOT NULL, DEFAULT `'Kesehatan'`| Category classification |
| `readingTimeMin`|`INTEGER` | NOT NULL, DEFAULT `3` | Estimated reading minutes |
| `isPublished` | `BOOLEAN` | NOT NULL, DEFAULT `true` | Public visibility flag |
| `publishedAt` | `TIMESTAMPTZ` | NOT NULL, DEFAULT `NOW()` | Publication release date |
| `metaTitle` | `VARCHAR(255)`| NULLABLE | Custom SEO title tag |
| `metaDesc` | `VARCHAR(500)`| NULLABLE | Custom SEO description tag |
| `viewCount` | `INTEGER` | NOT NULL, DEFAULT `0` | Organic engagement metric |

---

## 3. Complete Production Prisma Schema (`prisma/schema.prisma`)

```prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

enum OrderStatus {
  PENDING
  PROCESSING
  PAID
  CANCELLED
  EXPIRED
  REFUNDED
}

enum ProductType {
  DIGITAL_EBOOK
  DIGITAL_COURSE
  PHYSICAL_HERBAL
  SERVICE_CONSULTATION
}

enum AdminRole {
  SUPERADMIN
  EDITOR
}

// -------------------------------------------------------------
// 1 & 2. MENU PRODUCT & CATALOG
// -------------------------------------------------------------
model Product {
  id             String          @id @default(cuid())
  slug           String          @unique
  title          String
  headline       String?
  description    String          @db.Text
  price          Int             // IDR in integer (no floating-point rounding errors)
  comparePrice   Int?
  productType    ProductType     @default(DIGITAL_EBOOK)
  assetUrl       String?         // Protected storage path for digital PDF
  thumbnailUrl   String?
  features       String[]        // Array of highlights/bullet points
  isActive       Boolean         @default(true)
  sortOrder      Int             @default(0)
  createdAt      DateTime        @default(now())
  updatedAt      DateTime        @updatedAt

  orderItems     OrderItem[]
  downloadTokens DownloadAccess[]

  @@index([slug, isActive])
  @@index([sortOrder, isActive])
}

// -------------------------------------------------------------
// 3. MENU ARTIKEL (BLOG & EDUCATIONAL CONTENT)
// -------------------------------------------------------------
model Article {
  id             String    @id @default(cuid())
  slug           String    @unique
  title          String
  excerpt        String    @db.Text
  content        String    @db.Text
  coverImageUrl  String?
  category       String    @default("Kesehatan") // Diet, Nutrisi, Herbal, Lifestyle
  readingTimeMin Int       @default(3)
  isPublished    Boolean   @default(true)
  publishedAt    DateTime  @default(now())
  metaTitle      String?
  metaDesc       String?
  viewCount      Int       @default(0)
  createdAt      DateTime  @default(now())
  updatedAt      DateTime  @updatedAt

  @@index([slug, isPublished])
  @@index([category, isPublished])
  @@index([publishedAt(sort: Desc)])
}

// -------------------------------------------------------------
// ORDERS & FINANCIAL LEDGER
// -------------------------------------------------------------
model Order {
  id               String               @id @default(cuid())
  orderNumber      String               @unique // E.g., KND-20261029-XXXX
  customerName     String
  customerEmail    String
  customerPhone    String               // Formatted: +628...
  subtotal         Int
  discountAmount   Int                  @default(0)
  totalAmount      Int
  status           OrderStatus          @default(PENDING)
  voucherCode      String?
  notes            String?
  createdAt        DateTime             @default(now())
  updatedAt        DateTime             @updatedAt

  orderItems       OrderItem[]
  payments         PaymentTransaction[]
  downloadTokens   DownloadAccess[]

  @@index([orderNumber])
  @@index([customerEmail])
  @@index([customerPhone])
  @@index([status, createdAt])
}

model OrderItem {
  id        String   @id @default(cuid())
  orderId   String
  productId String
  title     String
  price     Int
  quantity  Int      @default(1)

  order     Order    @relation(fields: [orderId], references: [id], onDelete: Cascade)
  product   Product  @relation(fields: [productId], references: [id])

  @@index([orderId])
  @@index([productId])
}

model PaymentTransaction {
  id                 String      @id @default(cuid())
  orderId            String
  gatewayProvider    String      @default("MIDTRANS") // MIDTRANS | XENDIT
  gatewayReferenceId String      @unique // Midtrans order_id
  paymentType        String?     // qris, bank_transfer, gopay, etc.
  grossAmount        Int
  transactionStatus  String      // pending, settlement, expire, cancel
  signatureVerified  Boolean     @default(false)
  rawPayload         Json?
  paidAt             DateTime?
  createdAt          DateTime    @default(now())
  updatedAt          DateTime    @updatedAt

  order              Order       @relation(fields: [orderId], references: [id], onDelete: Cascade)

  @@index([orderId])
  @@index([gatewayReferenceId])
}

// -------------------------------------------------------------
// 4. MENU DOWNLOAD (DIGITAL ASSET DELIVERY)
// -------------------------------------------------------------
model DownloadAccess {
  id            String    @id @default(cuid())
  orderId       String
  productId     String
  token         String    @unique @default(uuid())
  downloadCount Int       @default(0)
  maxDownloads  Int       @default(5)
  expiresAt     DateTime  // 72 hours from payment settlement
  createdAt     DateTime  @default(now())

  order         Order     @relation(fields: [orderId], references: [id], onDelete: Cascade)
  product       Product   @relation(fields: [productId], references: [id])

  @@index([token])
  @@index([orderId])
}

// -------------------------------------------------------------
// VOUCHERS & PROMOTIONS
// -------------------------------------------------------------
model Voucher {
  id            String    @id @default(cuid())
  code          String    @unique
  discountType  String    @default("FIXED") // FIXED | PERCENTAGE
  discountValue Int
  minSpend      Int       @default(0)
  maxUses       Int       @default(100)
  currentUses   Int       @default(0)
  isActive      Boolean   @default(true)
  validUntil    DateTime?
  createdAt     DateTime  @default(now())

  @@index([code, isActive])
}

// -------------------------------------------------------------
// 5. CMS ADMIN USERS
// -------------------------------------------------------------
model AdminUser {
  id             String    @id @default(cuid())
  email          String    @unique
  name           String
  hashedPassword String
  role           AdminRole @default(SUPERADMIN)
  createdAt      DateTime  @default(now())
  updatedAt      DateTime  @updatedAt
}

// -------------------------------------------------------------
// 6. MENU PROFIL & SITE SETTINGS
// -------------------------------------------------------------
model SiteProfile {
  id               String   @id @default("DEFAULT")
  brandName        String   @default("Kanidee")
  tagline          String   @default("Buku e-digital Kesehatan praktis & Pelatihan Paripurna")
  bioStory         String   @db.Text
  missionStatement String   @db.Text
  whatsappNumber   String   @default("6281234567890")
  email            String   @default("halo@kanidee.com")
  instagramHandle  String?  @default("@kanidee")
  tiktokHandle     String?  @default("@kanidee")
  updatedAt        DateTime @updatedAt
}

model SecurityAuditLog {
  id        String   @id @default(cuid())
  eventType String   // WEBHOOK_VERIFIED, SIGNATURE_FAILED, DOWNLOAD_ATTEMPT, TAMPER_BLOCKED, ADMIN_LOGIN
  actor     String   @default("SYSTEM")
  ipAddress String?
  userAgent String?
  metadata  Json?
  createdAt DateTime @default(now())

  @@index([eventType, createdAt])
}
```

---

## 4. Seeding Strategy (`prisma/seed.ts`)

A deterministic seeding script establishes the baseline products matching the client's lynk.id catalog:

```typescript
import { PrismaClient } from '@prisma/client';
import bcrypt from 'bcryptjs';

const prisma = new PrismaClient();

async function main() {
  console.log('Seeding Kanidee database...');

  // 1. Seed Default Admin User
  const hashedPassword = await bcrypt.hash('KanideeSecure2026!', 10);
  await prisma.adminUser.upsert({
    where: { email: 'admin@kanidee.com' },
    update: {},
    create: {
      email: 'admin@kanidee.com',
      name: 'Kanidee Master Admin',
      hashedPassword,
      role: 'SUPERADMIN',
    },
  });

  // 2. Seed Baseline Products (from Lynk.id)
  await prisma.product.upsert({
    where: { slug: 'menu-diet' },
    update: {},
    create: {
      slug: 'menu-diet',
      title: 'Panduan Menu Diet Sehat 30 Hari',
      headline: 'Buku e-digital panduan praktis diet seimbang anti-kelaparan',
      description: 'Panduan lengkap pengaturan porsi kalori, jadwal makan teratur, dan resep lezat.',
      price: 99000,
      comparePrice: 149000,
      productType: 'DIGITAL_EBOOK',
      features: [
        '30 Variasi Resep Sarapan, Siang & Malam',
        'Panduan Menghitung Kalori Harian Otomatis',
        'Daftar Belanja Pasar Mingguan Hemat',
        'Format PDF Berwarna Siap Cetak / Buka di HP',
      ],
      sortOrder: 1,
    },
  });

  await prisma.product.upsert({
    where: { slug: 'menu-sehat' },
    update: {},
    create: {
      slug: 'menu-sehat',
      title: 'Program Pelatihan Menu Sehat Paripurna',
      headline: 'Edukasi mendalam sekaligus peluang penghasilan pelatihan mandiri',
      description: 'Kurikulum lengkap transformasi pola makan dan konsep pelatihan kesehatan terpadu.',
      price: 199000,
      comparePrice: 299000,
      productType: 'DIGITAL_COURSE',
      features: [
        'Akses Seluruh Modul E-Book & Video Panduan',
        'Studi Kasus Penurunan Berat Badan Berkelanjutan',
        'Grup Diskusi & Mentoring WhatsApp Eksklusif',
        'Sertifikat Penyelesaian Pelatihan Digital',
      ],
      sortOrder: 2,
    },
  });

  await prisma.product.upsert({
    where: { slug: 'maduka' },
    update: {},
    create: {
      slug: 'maduka',
      title: 'Paket Maduka & Panduan Herbal Alami',
      headline: 'Formula madu herbal murni pendamping metabolisme dan daya tahan tubuh',
      description: 'Kombinasi nutrisi alami Maduka yang diformulasikan khusus untuk detoksifikasi optimal.',
      price: 249000,
      comparePrice: 350000,
      productType: 'PHYSICAL_HERBAL',
      features: [
        '1 Botol Maduka Herbal Alami 350ml',
        'Bonus E-Book Panduan Konsumsi Terjadwal',
        'Bebas Pengawet & 100% Bahan Organik Pilihan',
        'Garansi Kualitas & Pengiriman Terlindungi',
      ],
      sortOrder: 3,
    },
  });

  console.log('Seeding completed successfully.');
}

main().catch(console.error).finally(() => prisma.$disconnect());
```
