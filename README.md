# Kanidee — Diet & Healthy Lifestyle Solution

Platform interaktif Kanidee yang menggabungkan produk kuliner sehat (*Frozen Bento tanpa bahan pengawet*, *Camilan Sehat Keripik Gadung*), serta produk edukasi gizi digital (*E-Book Resep Sehat Traditional Chinese Medicine*).

---

## 🌐 Live Interactive Demo

Mockup dan prototipe web interaktif dapat diakses langsung secara online:

- **🏪 Storefront Pelanggan:** [https://ademscil.github.io/kanidee/](https://ademscil.github.io/kanidee/)
- **⚙️ CMS Backoffice Merchant:** [https://ademscil.github.io/kanidee/docs/kanidee_cms_mockup.html](https://ademscil.github.io/kanidee/docs/kanidee_cms_mockup.html)

---

## 🍱 Katalog Produk Unggulan

### 1. Produk Kuliner Sehat & Frozen Bento (Tanpa Bahan Pengawet)
- **Kanirol Bento:** Olahan daging ayam dan rajungan renyah gurih berkualitas tinggi.
- **Egg Chicken Roll:** Daging ayam cincang olahan dengan gulungan telur lembut khas bento.
- **Shrimp Roll:** Perpaduan daging ayam dan udang segar berbalut tepung renyah.
- **Ekado:** Olahan daging ayam dan udang berbalut kulit kembang tahu dengan telur puyuh di dalamnya.
- **Spicy Chicken:** Nugget ayam berempah dengan sensasi pedas lezat.
- **Keripik Gadung Sehat (40g):** Camilan tradisional alami rendah kalori dan bebas kolesterol.

### 2. Produk Edukasi Digital
- **E-Book Resep Sehat Traditional Chinese Medicine (TCM):** Panduan 25 halaman formula nutrisi dan resep ramah pasar tradisional. Dilengkapi fitur instant direct download PDF.

---

## 📁 Struktur Repositori

```text
kanidee/
├── index.html                           # Entry-point redirect ke Storefront utama
├── README.md                            # Dokumentasi resmi repositori
├── KANIDEE_SDLC_MASTER_PLAN.md          # Dokumen master perencanaan SDLC
└── docs/
    ├── index.html                       # Fallback entry-point GitHub Pages
    ├── kanidee_storefront_mockup.html   # Halaman Storefront Pelanggan (Responsive)
    ├── kanidee_cms_mockup.html          # Backoffice Dashboard CMS & Reconcile Order
    ├── 01_BSD_Business_System_Design.md # Spesifikasi Bisnis & Alur Transaksi
    ├── 02_TRD_Technical_Requirements_Document.md
    ├── 03_AFD_Architectural_Functional_Design.md
    ├── 04_DBD_Database_Design_Document.md
    ├── 05_IPD_Integration_Payment_Design.md
    ├── 06_VTD_Verification_Testing_Delivery.md
    ├── 07_COST_INFRASTRUCTURE_DOMAIN_PLAN.md
    ├── 08_UI_UX_FULL_AUDIT_REPORT.md
    ├── 09_SDLC_MULTI_ROLE_AUDIT_STANDARD_V2.md
    └── assets/
        ├── img/                         # Logo Kanidee Lab SVG/PNG & E-Book Cover
        └── products/                    # Foto produk riil & Master File PDF TCM
```

---

## 🚀 Cara Menjalankan Secara Lokal

1. Clone repositori:
   ```bash
   git clone https://github.com/ademscil/kanidee.git
   cd kanidee
   ```
2. Buka berkas `index.html` atau `docs/kanidee_storefront_mockup.html` langsung di browser favorit Anda, atau gunakan live-server:
   ```bash
   npx serve .
   ```

