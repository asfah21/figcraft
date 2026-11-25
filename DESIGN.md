# DESIGN.md: Spesifikasi Visual Sanggar ERP untuk Template Next.js

**Tujuan:** template Next.js ini harus tampil 100% mengikuti desain **Sanggar ERP** di FigCraft. File ini adalah catatan tertulis dari canvas, bukan pengganti canvas. Jika ada perbedaan antara file ini dan canvas, **canvas yang menang**.

**Sumber kebenaran:** `C:\Users\LENOVO\Downloads\Sanggar ERP.figcraft` (terbuka di FigCraft). Gunakan `figcraft-app`. Acuan adalah frame berprefix `E ·` (versi terbaru). Frame `Lama ·` dan `Arsip Sidebar ·` adalah arsip dan **tidak boleh** dipakai sebagai acuan.

## 0. Aturan membaca canvas
- Baca `AGENTS.md` di root project sebelum bekerja. Cek versi Next.js, React, dan Tailwind di `package.json`, lalu baca guide yang relevan di `node_modules/next/dist/docs/` sebelum menulis kode.
- Satu sesi cukup membaca canvas sekali di awal. Koneksi FigCraft punya batas 60 detik per request: baca per frame, jangan seluruh dokumen sekaligus.
- Urutan ekstraksi yang wajib:
  1. `variable.export` dengan `format: "tailwind"` → daftar token warna persis.
  2. `get_node` pada frame yang disebut di bagian 3 → ukuran, posisi, teks, susunan.
  3. `inspect.code` dengan `language: "tailwind"` / `"react"` → pola kelas dan struktur; **tulis ulang dengan rapi**, jangan menempel kode export mentah.
  4. `screenshot` hanya untuk pengecekan akhir, bukan untuk menebak ukuran.
- Jangan mengubah, menyimpan ulang, atau memindahkan file Sanggar ERP. File ini hanya dibaca.
- Jangan memakai gaya portofolio lama: tidak ada latar `#0C0C0C`, aksen oranye `#F26A21`, Hero, Proyek, Tentang, atau Kontak.

## 1. Design Token

### 1.1 Warna (dari collection `Sanggar/Colors`, mode `Mode 1`)
Nama variable FigCraft di sebelah kiri, custom property Tailwind di kanan.

| Variable FigCraft | CSS variable | Hex |
|---|---|---|
| `brand/green` | `--color-green` | `#77B844` |
| `brand/green-deep` | `--color-green-deep` | `#3F7A1C` |
| `brand/green-soft` | `--color-green-soft` | `#ECFDF3` |
| `brand/blue` | `--color-blue` | `#2462AF` |
| `brand/blue-soft` | `--color-blue-soft` | `#EFF6FF` |
| `surface/dark` | `--color-dark` | `#1E5296` |
| `surface/card` | `--color-card` | `#FFFFFF` |
| `surface/subtle` | `--color-surface-subtle` | `#F7F9FC` |
| `surface/canvas` | `--color-canvas` | `#F4F7FA` |
| `border/subtle` | `--color-border-subtle` | `#EEF2F6` |
| `border/strong` | `--color-strong` | `#E2E8F0` |
| `text/primary` | `--color-primary` | `#111827` |
| `text/secondary` | `--color-secondary` | `#475569` |
| `text/muted` | `--color-muted` | `#94A3B8` |
| `text/on-dark` | `--color-on-dark` | `#FFFFFF` |
| `status/safe-text` | `--color-safe-text` | `#4E8A25` |
| `status/safe-bg` | `--color-safe-bg` | `#EEF7E6` |
| `status/warn-text` | `--color-warn-text` | `#B45309` |
| `status/warn-bg` | `--color-warn-bg` | `#FEF3C7` |
| `status/danger-text` | `--color-danger-text` | `#B91C1C` |
| `status/danger-bg` | `--color-danger-bg` | `#FEE2E2` |
| `status/stale-text` | `--color-stale-text` | `#4B5563` |
| `status/stale-bg` | `--color-stale-bg` | `#F1F5F9` |

**Aturan pakai warna:**
- Kanvas halaman `#F4F7FA`; kartu dan topbar `#FFFFFF`; latar kontrol/input `#F7F9FC`.
- Garis kartu dan tabel memakai hairline 1 px `#EEF2F6`; pemisah kuat/topbar `#E2E8F0`. Tanpa drop shadow tebal.
- Hijau `#77B844` untuk identitas merek dan aksi utama; `#3F7A1C` untuk teks aktif di atas `#ECFDF3`. Biru `#2462AF` hanya untuk informasi.
- Warna status hanya untuk badge/status. Jangan dipakai sebagai blok dekoratif besar.
- Di atas panel biru tua `#1E5296`, semua teks memakai `#FFFFFF`.

### 1.2 Tipografi
Font: **Plus Jakarta Sans** untuk teks UI, **JetBrains Mono** untuk kode dan angka tabel. Keduanya dimuat lewat `next/font/google`.

| Text style FigCraft | Font / weight | Ukuran / line-height | Pemakaian |
|---|---|---|---|
| `Sanggar/KPI Metric` | Plus Jakarta Sans Bold | 24 / 30 | Angka KPI besar |
| `Sanggar/Page Title` | Plus Jakarta Sans Bold | 20 / 26 | Judul halaman |
| `Sanggar/Card Title` | Plus Jakarta Sans SemiBold | 14 / 20 | Judul kartu |
| `Sanggar/Body Strong` | Plus Jakarta Sans SemiBold | 12 / 16 | Label dan aksi |
| `Sanggar/Body Medium` | Plus Jakarta Sans Medium | 12 / 16 | Isi sel tabel |
| `Sanggar/Label` | Plus Jakarta Sans Medium | 11 / 15 | Meta sekunder |
| `Sanggar/Badge` | Plus Jakarta Sans SemiBold | 11 / 14 | Status pill |
| `Sanggar/Helper` | Plus Jakarta Sans Regular | 10 / 14 | Bantuan form |
| `Sanggar/Data Mono` | JetBrains Mono Medium | 12 / 16 | Kode & angka tabel |
| `Sanggar/Data Mono SM` | JetBrains Mono Medium | 11 / 15 | Axis & kbd |
| `Sanggar/Section Caps` | Plus Jakarta Sans SemiBold | 10 / 14, letter-spacing +6% | Label grup navigasi |

Aturan tambahan dari canvas: judul dan nama aplikasi memakai `tracking-[-0.01em]`; angka tabel memakai font mono, tabular, dan rata kanan; huruf kapital kecil hanya untuk `Section Caps`.

### 1.3 Ukuran, jarak, radius
- Layar acuan: **1440 × 960 px**.
- Tombol: 36 px (kepala halaman/form) dan 28 px (panel kontrol/baris tabel). Input: 36 px. Kepala tabel: 36 px. Baris tabel: 40 px.
- Jarak: 4, 8, 12, 16, 24, 32 px. Jarak antar kartu 12–16 px; padding kartu 14–20 px.
- Radius: 6–8 px untuk kontrol, tabel, dan kartu operasi; 10–16 px untuk kartu/dialog besar; 20 px untuk KPI bento; lingkaran avatar 16 px.
- Border default: 1 px solid. Tidak boleh ada sudut melengkung pada elemen yang di canvas radiusnya 6 px.
- Ikon: **Tabler Icons** (MIT), outline, ukuran induk 16 × 16 px dengan garis 1 px (setara Tabler 24 px stroke 1,5 yang diperkecil). Ukuran turunan: 12 px (badge/centang), 14 px (tombol kecil/sel tabel), 16 px (bawaan), 20 px (kepala kartu), 28 px (ubin App Launcher). Ujung dan sambungan garis membulat, tanpa isian kecuali `StarFilled`. Warna mengikuti teks di sebelahnya; merah hanya untuk hapus, hijau untuk berhasil, kuning untuk peringatan. Nama komponen `Icon/<NamaTabler>`, SVG inline, jangan mencampur pustaka ikon lain.

## 2. Shell umum (berlaku di semua layar bertopbar)
- Setiap layar adalah frame 1440 × 960 dengan latar `--color-canvas`.
- `AppTopNav`: 1440 × 52, latar putih, border bawah 1 px `#E2E8F0`, padding kiri/kanan 20 px.
  - **Left** mulai x = 20, y = 9,5, tinggi 32: `AppLauncher` 32 × 32 r6 latar `#F7F9FC`; logo aplikasi 26 × 26 r6 latar `#77B844`; divider 1 × 20 `#E2E8F0`; menu mulai x = 179 dengan jarak 2 px, tiap item min-height 32 px, padding 12 px, radius 6 px. Item aktif: teks `#3F7A1C` latar `#ECFDF3`; item lain teks `#475569` + chevron 12 px.
  - **Right** mulai x = 852, y = 9,5, lebar 568, tinggi 32: `CommandSearch` 240 × 32 r6 berisi placeholder `Cari menu, dokumen…` dan kbd `Ctrl K`; tiga `IconButton` 32 × 32 r6 pada x = 252, 296, 340; `Company` 140 × 32 r6 berisi `Sanggar Kriya`; avatar `User` 32 × 32 r16 berisi inisial `BS`.
- **Body**: mulai y = 52, lebar penuh 1440. `ControlPanel` tinggi 85–98 px, lalu `Content` dengan padding 24 px. Isi dibatasi lebar 1392 px (x = 24 sampai 1416).
- Kartu: latar putih, border 1 px `#EEF2F6`, radius 6 px, tanpa shadow. Jarak antar kartu 16 px.
- **Penting:** layar `E ·` tidak memakai sidebar permanen. Navigasi modul ada di `AppTopNav`. Sidebar 220 px hanya ada di frame `Lama ·`/`Arsip Sidebar ·` dan tidak boleh ditambahkan ke template.

## 3. Daftar layar dan ukuran persis

### 3.1 `E · Core - Login` — frame 17194
- 1440 × 960. Terdiri dari `BrandPanel` 520 × 960 dan `Main` 920 × 960.
- **BrandPanel:** latar biru tua `#1E5296`, semua teks putih. Brand di x = 56, y = 56: kotak ikon 32 × 32 r8 + teks `Sanggar ERP` 20 px. Pitch di x = 56, y = 255, 408 × 468: ilustrasi 408 × 280, kalimat `Kelola stok, penjualan, pembelian, dan keuangan dari satu akun.` pada y = 300 (408 × 60), tiga baris poin pada y = 380, 416, 452 (tinggi 16). Footer `© 2026 Sanggar Labs · v2.3.1` di x = 56, y = 890.
- **Main:** `AuthCard` relatif terhadap Main di x = 260, y = 201, berukuran 400 × 523, radius 8, latar putih. Isi: Heading y = 33 (334 × 62), Field y = 111 (334 × 57), tombol utama y = 184 (334 × 36, r8), Divider y = 236 (334 × 15), empat `SSOButton` 334 × 36 r6 pada y = 267, 319, 371, 423, teks bantuan `Belum punya akun? Minta undangan dari administrator Anda.` pada y = 475. Footer kartu di x = 296, y = 744 berisi `Bahasa`, `Bantuan`, `Privasi`, `Status sistem`.
- **Cek:** panel kiri benar-benar 520 px, kartu login terpusat di panel kanan, tidak ada sidebar/topbar di halaman login.

### 3.2 `E · Core - App Launcher` — frame 8232
- 1440 × 960. `Body` 1440 × 464 berisi `AppTopNav` 52 px dan `Content` 412 px.
- Kolom pencarian utama: `Cari aplikasi, menu, atau dokumen…`.
- Ubin aplikasi: `Inventory`, `Sales`, `Pembelian`, `Manufaktur`, `Finance`, `CRM`, `Point of Sale`, `Proyek`, `SDM`, `Laporan & BI`.
- Tautan bawah: `Aplikasi` dan `Pengaturan`.
- **Cek:** tidak ada sidebar; ubin memakai ikon 28 px dan kartu r12–16 sesuai komponen `ModuleAppCard`.

### 3.3 `E · Core - Dashboard` — frame 12546
- `ControlPanel` 1440 × 85: `Widget`, `Bulan Ini`, kolom cari `Cari di dashboard…`, periode `Nov 2026`, aksi `Filter`, `Kelompokkan`, `Favorit`, serta keterangan `Diperbarui 2 menit lalu`.
- `Content` 1440 × 639, padding 24. Kartu KPI:
  - `Pendapatan` — `Rp 1,28 M`, `+12,4% dari Okt`
  - `Pesanan Terbuka` — `64`, `18 jatuh tempo minggu ini`
  - `Nilai Persediaan` — `Rp 486,2 jt`, `+4,1%`
  - `Menunggu Persetujuan` — `7`, `2 lewat SLA`
- Panel: `Aktivitas Saya`, `Aplikasi Terpasang` (Inventory, Sales, Pembelian, Manufaktur, Finance), `Kesehatan Sistem`, `Pengumuman`.
- Teks pengumuman persis: `Stock opname akhir tahun dijadwalkan 28-29 Desember. Penerimaan dan pengiriman dibekukan selama periode hitung.`
- **Cek:** empat KPI sejajar, angka memakai JetBrains Mono, panel tidak saling menimpa.

### 3.4 `E · Inventory - Overview` — frame 36838
- `ControlPanel` 1440 × 98: tombol `Baru`, konteks `Gudang Utama`, pencarian `Cari jenis operasi…`, pager `1-6 / 6`, aksi `Filter`, `Kelompokkan`, `Favorit`, ringkasan `6 jenis operasi · 27 dokumen untuk diproses · 3 terlambat`.
- `Content` 1440 × 592, padding 24.
  - `OperationGrid` x = 24, y = 20, 1392 × 272: enam `OperationCard` 453 × 128, radius 6, padding 17, kolom x = 0/469/938, baris y = 0/144, jarak 16. Tiap kartu punya `Header` 419 × 37 (y = 15) dan `Body` 419 × 49 (y = 64).
  - `Panels` x = 24, y = 308, 1392 × 264: `Stok Perlu Perhatian` 996 × 264 (judul x = 17, y = 17; `DataTable` x = 17, y = 49, 962 × 198) dan `Aktivitas Terakhir` 380 × 192 (tiga `ChatterMessage` 346 × 34 pada y = 49, 95, 141).
- **Cek:** enam kartu operasi terdistribusi rata, panel bawah sejajar, tidak ada celah kosong di kanan.

### 3.5 `E · Inventory - Produk (List)` — frame 37292
- `ControlPanel` 1440 × 98: `MainRow` x = 24, y = 13, 1392 × 34; `FilterRow` x = 24, y = 57, 1392 × 28.
- Teks: `Baru`, `Dapat Disimpan`, `Cari nama, SKU, barcode…`, `1-12 / 248`, `2 dipilih`, `Aksi`, `Ekspor`, `Filter`, `Kelompokkan`, `Favorit`, `248 produk · 9 di bawah minimum`.
- `Content` 1440 × 633; `DataTable` x = 24, y = 20, 1392 × 593, radius 6.
- Kolom: `SKU`, `Kategori`, `Tersedia`, `Perkiraan`, `Satuan`, `Biaya`, `Nilai Stok`, `Status`.
- Baris demo pertama: `Kain Tenun Sumba 200cm` · `SGR-KRY-0042` · `Kerajinan / Tenun` · `128` · `140` · `lembar` · `Rp 185.000` · `Rp 23.680.000`. Baris lain: `Benang Katun Indigo`, `Keranjang Rotan M`, `Pewarna Alam Secang`, `Topeng Kayu Malangan`, `Batik Tulis Lasem 2m`, `Malam Batik Klowong`.
- **Cek:** angka rata kanan dan mono; tinggi baris 40 px; status memakai `StatusBadge` dengan token status.

### 3.6 `E · Inventory - Produk (Form)` — frame 37944
- `FormHeader` 1440 × 98: `TitleRow` x = 24, y = 13, 1392 × 34; `ActionRow` x = 24, y = 57, 1392 × 28.
- Breadcrumb `Inventory / Produk`; judul `Kain Tenun Sumba 200cm`; penanda `1 / 248`.
- Aksi: `Simpan`, `Perbarui Stok`, `Isi Ulang`, `Cetak Label`.
- `Content` 1440 × 780: `Main` x = 24, y = 20, 1036 × 740; `Chatter` x = 1076, y = 20, 340 × 269, radius 6.
- Grup pada Main: `LOGISTIK`, `STOK PER LOKASI`; tab `Catatan` dan `Aktivitas`.
- Aktivitas demo: `Stok diperbarui: 8 → 128 lembar (WH/IN/00042)`, `Biaya diubah: Rp 180.000 → Rp 185.000`, `Aturan reorder dibuat: min 20, maks 150`, `Produk dibuat`.
- **Cek:** kolom utama 1036 px dan chatter 340 px terpisah 16 px; tab tidak bergeser saat konten panjang.

### 3.7 `E · Inventory - Penerimaan (List)` — frame 38315
- `ControlPanel` 1440 × 98 dengan `MainRow` dan `FilterRow` seperti 3.5.
- Breadcrumb `Inventory / Operasi`; judul `Penerimaan`; status `Siap`; pencarian `Cari referensi, pemasok, dokumen sumber…`; `1-10 / 42`; `2 dipilih`; `Aksi`; `Ekspor`; ringkasan `42 dokumen · 12 siap · 1 terlambat`.
- `DataTable` x = 24, y = 20, 1392 × 473, radius 6. Kolom: `Referensi`, `Dari`, `Ke`, `Dokumen Sumber`, `Jadwal`, `Penanggung Jawab`, `Status`.
- Baris demo pertama: `WH/IN/00042` · `Koperasi Tenun Kambera` · `WH/Stok` · `PO-0319` · `22 Nov 2026` · `Siti Rahayu`. Baris berikutnya `WH/IN/00041` (`CV Benang Nusantara`, `PO-0318`) dan `WH/IN/00040` (`UD Rotan Cirebon`, `PO-0315`, `20 Nov 2026`, `Agus Wijaya`).
- Teks kaki tabel: `Menampilkan 1-10 dari 42 penerimaan`.
- **Cek:** tabel lebih pendek dari halaman produk (473 vs 593), sesuai canvas; jangan dipaksa sama tinggi.

### 3.8 State dropdown — frame 43090
- `TopNavDropdown` x = 281, y = 48, 232 × 148, radius 8, latar putih, border hairline.
- Enam `MenuItem` 218 × 32, radius 6, pada x = 7 dan y = 7, 41, 75, 109, 143. Label + count: `Penerimaan 12`, `Pengiriman 8`, `Transfer Internal 4`, `Penyesuaian Stok 2`, `Item 0`, `Item 0`.
- Dropdown menimpa Body, bukan menggeser konten.

## 4. Responsif dan batasan acuan
- File Sanggar ERP hanya menyediakan frame 1440 × 960. Tidak ada frame Mobile/tablet untuk layar `E ·`.
- Di bawah 1024 px, template boleh menyusun ulang menjadi satu kolom, tetapi **wajib** mempertahankan semua token, radius, tipografi, dan komponen di atas. Ini adalah fallback, bukan klaim kemiripan 100%; cantumkan di laporan akhir.
- Di 1024 px ke atas, seluruh ukuran mengikuti angka di bagian 2 dan 3 tanpa scaling manual.

## 5. Pengecekan akhir
1. Pastikan setiap angka di komponen berasal dari canvas atau token; tidak ada hex atau ukuran baru.
2. Bandingkan struktur tiap halaman dengan frame ID yang bersangkutan via `inspect_document`/`get_node`, bukan dengan perkiraan.
3. Pastikan tidak ada gaya portofolio lama, sidebar pada layar `E ·`, atau ikon di luar Tabler.
4. Laporan akhir berisi: file yang dibuat, tujuh layar yang tercapai, perbedaan terhadap canvas, dan bagian responsif yang tidak memiliki acuan canvas.
