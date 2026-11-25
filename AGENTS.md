<!-- BEGIN:nextjs-agent-rules -->

## This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` (resolved from this file's directory; in monorepos the `next` package may not be visible from the repo root) before writing any code. Heed deprecation notices.

This block is written and re-added by `next dev` — verify at `node_modules/next/dist/server/lib/generate-agent-files.js`. Removing it from a diff only re-creates the uncommitted change; committing it with your work keeps the tree clean.

<!-- END:nextjs-agent-rules -->

## Aturan proyek: Sanggar ERP

- Acuan tampilan tunggal adalah file FigCraft `Sanggar ERP` (`C:\Users\LENOVO\Downloads\Sanggar ERP.figcraft`). Pakai `figcraft-app` dan hanya frame berprefix `E ·`. Frame `Lama ·` dan `Arsip Sidebar ·` adalah arsip, bukan acuan.
- Sebelum menulis kode, baca `DESIGN.md`, `WEBSITE.md`, dan guide Next.js versi terpasang di `node_modules/next/dist/docs/`. Versi saat ini: Next.js 16.4.6, React 19.3.0, Tailwind 4.3.3.
- Semua warna, tipografi, jarak, dan radius harus berasal dari variables FigCraft (`variable.export`, format Tailwind). Dilarang menulis hex atau ukuran baru di komponen. Dilarang memakai gaya portofolio lama (latar `#0C0C0C`, aksen oranye `#F26A21`, Hero/Proyek/Tentang/Kontak).
- Ekstraksi wajib: `variable.export` untuk token, `get_node` untuk ukuran/teks per frame, `inspect.code` untuk struktur. Tulis ulang kode export menjadi komponen rapi; jangan menempel kode mentah.
- Ikon memakai SVG inline dari Tabler Icons (MIT), outline 16 px stroke 1 px, sesuai aturan `DESIGN.md`. Jangan mencampur pustaka ikon lain.
- Template diekspor statis (`output: 'export'`). Jangan memakai Server Action, API route, middleware, `cookies()`, atau `headers()`.
- Jangan mengubah `public/`, `referensi/`, `DESIGN.md`, `WEBSITE.md`, atau file FigCraft tanpa izin. Jangan menjalankan `git commit`/`git push`.
- Laporan akhir wajib menyebut frame ID yang dipakai, file yang diubah, dan perbedaan terhadap canvas beserta alasannya.
