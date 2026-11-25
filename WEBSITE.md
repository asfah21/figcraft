# WEBSITE.md: Membangun Template Next.js Sanggar ERP dari Desain FigCraft

**Tugas:** ubah template Next.js ini menjadi antarmuka **Sanggar ERP** yang tampil 100% mengikuti frame `E ·` di file FigCraft `Sanggar ERP`. Data bersifat demo dan berjalan di browser; tidak ada database maupun server khusus. Website diekspor sebagai HTML statis.

**Acuan:** `DESIGN.md` adalah catatan tertulis dari canvas. Jika keduanya berbeda, **canvas FigCraft yang menang**. Gunakan `figcraft-app` untuk membaca token dan frame, lalu tulis ulang kode dengan rapi.

**Urutan kerja (kerjakan semuanya tanpa berhenti di tiap fase):**
1. Persiapan dan ekstraksi desain (bagian 1).
2. Fondasi: config statis, token, font, struktur folder, konstanta (bagian 2 dan 3).
3. Bangun shell dan tujuh layar (bagian 4).
4. Interaksi utama (bagian 5).
5. Aksesibilitas dan performa (bagian 6).
6. Pengecekan akhir dan laporan (bagian 7).

**Cara kerja (wajib):**
- Baca `AGENTS.md` dan `DESIGN.md` di root project. Cek `package.json` untuk versi Next.js, React, dan Tailwind yang terpasang, lalu baca guide relevan di `node_modules/next/dist/docs/` sebelum menulis kode. Versi yang terpasang adalah Next.js 16.4.6, React 19.3.0, Tailwind 4.3.3 — jangan mengandalkan pola lama.
- Baca canvas sekali di awal: `variable.export` (Tailwind), lalu `get_node` per frame, lalu `inspect.code` (Tailwind/React). Koneksi FigCraft punya batas 60 detik per request, jadi baca per frame, bukan seluruh dokumen.
- Tulis banyak file sekaligus dalam satu tool call (misalnya semua komponen shell sekaligus, lalu semua komponen satu layar sekaligus). Jangan membaca ulang file yang baru ditulis kecuali ada error.
- Jalankan `npm run lint` dan `npm run build` satu kali di akhir. Ulangi hanya kalau ada error.
- Jangan menjalankan dev server atau mengambil screenshot kecuali perlu memperbaiki masalah.
- Jangan mengubah file Sanggar ERP di FigCraft, `DESIGN.md`, `AGENTS.md`, folder `public/`, atau folder `referensi/`. Jangan menjalankan `git commit`/`git push`.

## 1. Ekstraksi dari FigCraft
- Pastikan file aktif adalah `Sanggar ERP` (`C:\Users\LENOVO\Downloads\Sanggar ERP.figcraft`). Kalau tidak, buka file itu dengan `figcraft-app`.
- Jalankan `variable.export` dengan `format: "tailwind"` dan salin seluruh blok `@theme static` ke `app/globals.css`. Jangan menulis ulang hex secara manual.
- Untuk tiap frame di bagian 4, jalankan `get_node` dan `inspect.code` (`language: "tailwind"`). Catat ukuran, posisi, radius, teks, dan hierarki. Kode export hanya referensi: ubah menjadi komponen yang rapi, pisahkan data demo ke `constants/`.
- Frame yang dipakai: `17194`, `8232`, `12546`, `36838`, `37292`, `37944`, `38315`, dan `43090` untuk state dropdown. Abaikan frame `Lama ·` dan `Arsip Sidebar ·`.
- Kalau `figcraft-app` gagal, tampilkan error aslinya, lalu lanjut memakai angka di `DESIGN.md` dan sebutkan hal itu di laporan akhir.

## 2. Konfigurasi dan batasan statis
- Di `next.config.ts`, tambahkan `output: 'export'` dan `images: { unoptimized: true }`. Pertahankan opsi `experimental`, `cacheComponents`, `partialPrefetching`, dan `turbopack` yang sudah ada.
- Hasil build ada di folder `out/`. Semua rute harus statis: gunakan `generateStaticParams` untuk rute dinamis.
- Dilarang: Server Actions (`'use server'`), API route, middleware, `cookies()`, `headers()`, rewrites/redirects di config, dan data fetching yang bergantung pada server saat runtime. Semua state dan interaksi berjalan di browser.
- `next/image` tetap boleh dipakai untuk ilustrasi, tetapi file di `public/` tidak diubah. Tidak ada gambar portofolio lama yang dipakai.

## 3. Struktur folder, token, dan font
Struktur yang harus ada:
- **`app/`**
  - `layout.tsx`: font, metadata, bahasa, struktur dasar
  - `globals.css`: `@theme` dari `variable.export` + gaya global
  - `page.tsx`: Dashboard (`E · Core - Dashboard`)
  - `login/page.tsx`: layar Login
  - `aplikasi/page.tsx`: App Launcher
  - `inventory/page.tsx`: Inventory Overview
  - `inventory/produk/page.tsx`: Daftar Produk
  - `inventory/produk/[id]/page.tsx`: Form/Detail Produk
  - `inventory/penerimaan/page.tsx`: Daftar Penerimaan
  - `inventory/layout.tsx`: shell topbar untuk rute bertopbar
- **`components/`**
  - `layout/`: `AppTopNav`, `TopNavDropdown`, `CommandSearch`, `PageHeaderBar`, `ControlPanel`, `BreadcrumbBar`
  - `sections/`: `LoginScreen`, `AppLauncher`, `Dashboard`, `InventoryOverview`, `ProductList`, `ProductForm`, `ReceiptList`
  - `ui/`: `Button`, `IconButton`, `TextField`, `SelectField`, `SearchInput`, `FilterChip`, `StatusBadge`, `CountBadge`, `MetricCard`, `OperationCard`, `DataTable`, `PaginationBar`, `ModalDialog`, `ToastAlert`, `Chatter`, `Tabs`, `icons` (SVG inline Tabler)
- **`hooks/`**: `useCommandPalette`, `useDropdown`, `useMockTable`, `useToast`, `useReducedMotion`
- **`constants/`**: `siteConfig`, `navigation`, `tokens`, `mockProducts`, `mockReceipts`, `mockDashboard`
- **`utils/`**: `formatCurrency`, `formatMonoNumber`, `formatDate` (fungsi murni)

Aturan kode:
- Satu komponen satu tanggung jawab. Nama camelCase untuk fungsi/variabel, PascalCase untuk komponen dan file komponen.
- TypeScript strict, hindari `any`. Beri komentar singkat pada fungsi dan logika penting.
- Semua nilai tetap (nama aplikasi, menu, judul, data demo, durasi animasi) ditaruh di `constants/`, bukan di dalam komponen.
- Fungsi yang bisa gagal mengembalikan `{ data, error }`. Setiap `async` memakai try/catch.
- Hapus kode mati dan sisa template `create-next-app` (`next.svg`, `vercel.svg`, teks "Create Next App", font Geist).
- Jangan menambah library besar. Ikon dibuat sebagai SVG inline Tabler (MIT) sesuai aturan `DESIGN.md`. Tidak perlu library animasi; efek memakai CSS dan hook sendiri.

## 4. Token, font, dan gaya global
- Tempel blok `@theme static` hasil `variable.export` ke `app/globals.css`. Komponen memakai token seperti `bg-canvas`, `bg-card`, `border-subtle`, `text-primary`, `text-green-deep`, `bg-green-soft`. Dilarang hex atau ukuran piksel baru di komponen.
- Satu-satunya token tambahan yang diizinkan: `error` (`#B91C1C`), hanya untuk pesan galat form.
- Font: `Plus Jakarta Sans` dan `JetBrains Mono` lewat `next/font/google`, dengan CSS variable yang dipetakan ke `@theme`. Jangan install font manual ke OS untuk keperluan website.
- Gaya global: latar `#F4F7FA`, teks `#111827`, `overflow-x: clip` pada body, `scroll-behavior: smooth`, fokus `focus-visible` memakai `#77B844`.
- Ukuran dan jarak mengikuti `DESIGN.md` bagian 1.3. Tidak ada `clamp()` untuk layar 1024 px ke atas: angka canvas dipakai persis.
- Di bawah 1024 px, susun satu kolom dengan token yang sama; ini fallback dan harus dicatat di laporan akhir.

## 5. Seksi dan rute
Ikutkan urutan, ukuran, dan teks persis seperti `DESIGN.md` bagian 3. Ringkasan:

| Rute | Frame | Isi utama |
|---|---|---|
| `/login` | 17194 | BrandPanel 520 + AuthCard 400 × 523 |
| `/aplikasi` | 8232 | pencarian aplikasi + 10 ubin modul |
| `/` | 12546 | Dashboard: ControlPanel 85, 4 KPI, 4 panel |
| `/inventory` | 36838 | ControlPanel 98, 6 OperationCard, 2 panel bawah |
| `/inventory/produk` | 37292 | ControlPanel 98 + DataTable 1392 × 593 |
| `/inventory/produk/[id]` | 37944 | FormHeader 98, Main 1036 × 740, Chatter 340 × 269 |
| `/inventory/penerimaan` | 38315 | ControlPanel 98 + DataTable 1392 × 473 |

- Shell `AppTopNav` 1440 × 52 dipakai di semua rute kecuali `/login`. Tidak ada sidebar pada layar `E ·`.
- Teks demo (nama produk, SKU, referensi, angka, tanggal, pengumuman) disalin persis dari canvas ke `constants/`. Jangan mengarang data baru.
- Menu aktif di topbar memakai `#3F7A1C` di atas `#ECFDF3`. State dropdown mengikuti frame 43090.
- Pencocokan: setiap selesai satu layar, bandingkan strukturnya dengan `get_node` frame terkait. Jangan mengubah bagian yang sudah benar.

## 6. Interaksi utama
- **AppTopNav:** klik `Menu` / item modul membuka `TopNavDropdown` 232 × 148; klik di luar atau Esc menutup. Item dropdown menuju rute yang sesuai dan menampilkan count dari `constants/navigation.ts`.
- **CommandSearch:** klik atau `Ctrl+K` membuka palet perintah berisi menu dan dokumen demo. Esc menutup. Fokus pindah ke input saat dibuka dan kembali ke trigger saat ditutup.
- **ControlPanel:** tombol `Baru`, `Filter`, `Kelompokkan`, `Favorit`, dan `Ekspor` berstatus mock; klik menampilkan `ToastAlert` atau membuka `ModalDialog`. Tidak boleh ada tautan mati.
- **Tabel:** pencarian, sumbangan centang (`2 dipilih`), sort kolom, dan paginasi (`1-12 / 248`, `1-10 / 42`) berjalan dengan data mock di browser. Angka memakai JetBrains Mono dan rata kanan.
- **Form/Detail Produk:** tab `Catatan` dan `Aktivitas` bisa dipilih. Tombol `Simpan`, `Perbarui Stok`, `Isi Ulang`, dan `Cetak Label` menampilkan toast atau dialog konfirmasi. Tidak ada pengiriman ke server.
- **Login:** input-email dan kata sandi memakai validasi sederhana di browser; tombol masuk menuju `/` bila terisi. Empat `SSOButton` menampilkan toast “SSO belum tersedia di demo”.
- **Toast:** muncul di kanan atas, hilang otomatis setelah 2 detik, dan bisa ditutup manual.
- **Akses keyboard:** urutan fokus logis, `aria-expanded` untuk dropdown, `aria-label` untuk ikon, dan fokus terkunci di modal/dropdown saat terbuka.
- **Gerak:** saat `prefers-reduced-motion: reduce`, matikan transisi transform; tampilan tetap identik.

## 7. Aksesibilitas, SEO, performa
- `<html lang="id">`. Satu `<h1>` per halaman (judul layar); gunakan `<h2>` untuk judul panel dan `<h3>` untuk kartu.
- Elemen semantik: `<header>`, `<nav>`, `<main>`, `<section>`, `<table>`, `<footer>`. Beri `aria-labelledby` pada tiap seksi.
- Metadata di `app/layout.tsx`: `title`, `description` bahasa Indonesia, Open Graph, `themeColor` `#F4F7FA`, diambil dari `constants/siteConfig.ts`.
- Kontras teks memenuhi keterbacaan. Area sentuh minimal 44 px di layar kecil.
- Semua gambar/ilustrasi memakai `next/image` dengan `alt` deskriptif dan ukuran tetap untuk menghindari layout shift.
- Input yang ditampilkan kembali ke UI tidak pernah dirender sebagai HTML mentah.

## 8. Pengecekan akhir dan laporan
Sebelum melapor:
1. Jalankan `npm run lint` dan `npm run build`. Perbaiki semua error/peringatan sampai bersih. Pastikan `out/index.html` dan `out/login/index.html` ada.
2. Satu kali grep: pastikan tidak ada `'use server'`, API route, font Geist, teks portofolio lama, hex di luar token, atau komponen/hook yang tidak dipakai.
3. Bandingkan tiap rute dengan frame ID-nya lewat `inspect_document`/`get_node`: ukuran shell, ControlPanel, grid, tabel, dan teks demo harus cocok.
4. Cek tidak ada scroll horizontal di 390, 1024, dan 1440 px. Untuk 390 px, nyatakan di laporan bahwa acuan canvas tidak tersedia.
5. Cek interaksi utama: dropdown, palet `Ctrl+K`, filter/tabel, tab, modal, dan toast.

Laporan akhir (singkat) berisi:
- Daftar file yang dibuat dan diubah.
- Tujuh layar yang berhasil dibuat beserta frame ID-nya.
- Fitur dan interaksi yang berhasil, dan yang tidak bisa dibuat beserta alasannya.
- Perbedaan terhadap canvas (kalau ada) dan keterbatasan responsif di bawah 1024 px.
- Nilai placeholder yang perlu diganti (nama perusahaan, inisial pengguna, data demo, tautan SSO).
- Cara menjalankan (`npm run dev`) dan lokasi build statis (`out/`).
