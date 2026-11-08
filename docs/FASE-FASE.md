# Panduan Fase-Fase: Duplikasi `/frontend` ke HTML5 Murni

Tujuan: menduplikasi UI `frontend` dari [aumo-finance](https://github.com/rdmmoonlight/aumo-finance/tree/main/frontend) ke HTML5 + CSS + JavaScript biasa, dengan tampilan **100% identik**.

Urutan kerja: **dari yang termuda (komponen terkecil) ke yang terbesar (halaman penuh)**. Setiap fase harus selesai dan lulus checklist sebelum fase berikutnya dimulai.

Rujukan sumber: `aumo-finance/frontend/src`.

---

## Temuan Awal (Wajib Dibaca)

1. **Warna di komponen vanilla lama tidak identik.** File `src/components/top-bar`, `nav-menu`, dan `footer` di repo rujukan memakai warna terang (`hsl(0 0% 100%)`). Aplikasi aslinya bertema **dark matte** (lihat `src/styles/theme.css`). Jadikan file itu bahan awal saja, bukan standar.
2. **Gunakan class Tailwind yang sama persis.** Aplikasi asli memakai Tailwind v4. Cara paling akurat: salin string `className` dari file `.tsx` ke atribut `class` HTML, lalu compile CSS dengan Tailwind CLI. Hasil akhirnya tetap HTML5 murni (CSS statis, tanpa framework saat berjalan).
3. **Ukuran sidebar punya keunikan.** Wrapper layout memakai `--sidebar-width: 285px` dan `--sidebar-width-icon: 3.5rem`, sedangkan kontainer sidebar memakai `255px` dan `4rem`. Dua nilai ini harus ditiru apa adanya agar tata letak sama.
4. **Tema.** Library `next-themes` menyimpan pilihan di `localStorage` dengan kunci `theme` dan memasang class `dark` atau `light` pada `<html>`. Default: ikut sistem.
5. **Breakpoint mobile = 768px.** Sidebar desktop berkelas `hidden md:block`.
6. **Font:** Inter (Google Fonts), bobot variabel 100–900.

---

## Struktur Folder Target

```
docs/
├── index.html              # Landing
├── home.html ...           # Satu file per route
├── assets/
│   ├── css/input.css       # Sumber (token + @import tailwindcss)
│   ├── css/app.css         # Hasil compile (di-commit agar GitHub Pages jalan)
│   ├── js/                 # Satu file per perilaku (theme.js, sidebar.js, dst.)
│   └── img/favicon.*       # Salin dari frontend/public
└── FASE-FASE.md            # Dokumen ini
```

Folder `docs/` dipilih agar bisa langsung dipublikasikan lewat GitHub Pages (Settings → Pages → Branch `main`, folder `/docs`).

---

## Fase 0 — Persiapan

1. Clone repo rujukan ke folder terpisah untuk dibaca: `git clone https://github.com/rdmmoonlight/aumo-finance`.
2. Buat struktur folder `docs/` di atas.
3. Salin `frontend/public/favicon.ico` dan `favicon.png` ke `docs/assets/img/`.
4. Pasang Tailwind CLI: `pnpm add -D tailwindcss @tailwindcss/cli tw-animate-css`.
5. Tambahkan script di `package.json`:
   - `"build:css": "tailwindcss -i ./docs/assets/css/input.css -o ./docs/assets/css/app.css --minify"`
   - `"watch:css": "tailwindcss -i ./docs/assets/css/input.css -o ./docs/assets/css/app.css --watch"`
6. Tambahkan `node_modules/` ke `.gitignore`.

**Checklist:** `pnpm build:css` berjalan tanpa error.

---

## Fase 1 — Fondasi Desain

Sumber: `src/styles/theme.css` dan `src/styles/index.css`.

1. Buat `docs/assets/css/input.css`. Isi dengan konten `index.css` dan `theme.css` (gabungkan, hapus `@import "./theme.css"` lalu letakkan isinya langsung).
2. Tambahkan `@source "../../../docs/**/*.html";` agar Tailwind memindai file HTML.
3. Pertahankan: token warna, radius, ukuran teks, kelas `matte-card`, `matte-hover`, `glass`, scrollbar, dan aturan selection.
4. Buat `docs/assets/js/theme.js`: baca `localStorage.theme`, jika kosong ikuti `prefers-color-scheme`, lalu pasang class `dark` atau `light` pada `<html>`. Panggil di `<head>` sebelum render agar tidak berkedip.
5. Buat `docs/components.html` sebagai halaman uji (galeri) untuk semua komponen berikutnya.

**Checklist:** warna latar `hsl(0 0% 5.5%)` pada mode gelap, font Inter aktif, pergantian tema berfungsi.

---

## Fase 2 — Komponen Dasar (Primitif UI)

Sumber: `src/components/ui/*.tsx`. Kerjakan dari yang paling sederhana. Untuk setiap komponen: salin string class, buat semua varian, lalu tampilkan di `components.html`.

| Urutan | Komponen | Catatan |
|---|---|---|
| 1 | separator, skeleton, label | Tanpa perilaku JS |
| 2 | badge | Varian: default, secondary, destructive, outline, ghost, link |
| 3 | button | Varian × ukuran (default, xs, sm, lg, icon, icon-xs, icon-sm, icon-lg) |
| 4 | input, textarea, checkbox, radio-group | State fokus, invalid, disabled |
| 5 | avatar | Gambar + fallback inisial |
| 6 | card, alert, table | Struktur saja |
| 7 | breadcrumb | Pemisah memakai ikon chevron |
| 8 | tooltip | JS: tampil saat hover/fokus |
| 9 | popover, dropdown-menu | JS: buka/tutup, klik di luar, tombol Esc, posisi `side` dan `align` |
| 10 | dialog, alert-dialog, sheet | JS: overlay, kunci scroll, fokus, Esc |
| 11 | tabs, collapsible, toggle, toggle-group | JS: atribut `data-state` |
| 12 | select, scroll-area, navigation-menu, sonner (toast) | Paling kompleks, terakhir |

Aturan teknis:
- Ikon: salin SVG dari Lucide (versi 1.47) dan sisipkan inline. Ukuran mengikuti class (`size-4`, `h-4 w-4`).
- Animasi `animate-in` dan `data-[state=open]` berasal dari `tw-animate-css`. Atur `data-state="open|closed"` lewat JS, jangan dengan class tambahan.
- Satu perilaku = satu fungsi JS kecil yang bekerja lewat atribut `data-*`, bukan lewat ID.

**Checklist:** setiap komponen di `components.html` dibandingkan berdampingan dengan Storybook/halaman asli pada mode gelap dan terang.

---

## Fase 3 — Komponen Shell (Kerangka Aplikasi)

Kerjakan berurutan.

### 3.1 Footer
Sumber: `src/components/app-footer.tsx`. Statis; tahun diisi `new Date().getFullYear()`.

### 3.2 Top Bar
Sumber: `src/components/app-topbar.tsx`.
- Bar utama: input pencarian (`pl-9 bg-muted/40`), tombol notifikasi, popover `w-80 p-0 mr-4`.
- Bar sekunder: breadcrumb dinamis dari `location.pathname`, badge periode, indikator status DB.
- Logika status DB: `online`, `connecting`, `offline`. Animasi: `animate-pulse`, `animate-ping`.
- Data awal memakai data contoh (mock) di Fase ini. Integrasi API dikerjakan di Fase 9.

### 3.3 Sidebar
Sumber: `src/components/app-sidebar.tsx` dan `src/components/ui/sidebar.tsx`.
- Struktur: wrapper `group peer hidden md:block` + elemen `sidebar-gap` + `sidebar-container` + `sidebar-inner`.
- State `expanded`/`collapsed` disimpan di atribut `data-state` dan `data-collapsible="icon"` pada wrapper.
- Perilaku: tombol collapse, rail, pintasan `Ctrl/Cmd + B`, cookie `sidebar_state` (7 hari), tooltip saat collapsed, dropdown pengguna (Settings, Sign Out).
- Menu: Home, Dashboard, Periods, Chart of Accounts, Journal Entry, Reports, Tools, Settings. Logika aktif: path sama persis, atau diawali `url + "/"` (kecuali `/home`).

### 3.4 Layout Terautentikasi
Sumber: `src/components/layouts/authenticated-layout.tsx`.
- Susunan: `SidebarProvider` → `AppSidebar` + `SidebarInset` (`TopBar` + `main.relative.flex-1.p-6` + `Footer`).
- Buat satu template `docs/_layout-template.html` sebagai acuan salin untuk semua halaman terautentikasi.
- Teks status: "Memverifikasi sesi...", "Memuat periode...".

**Checklist:** lebar sidebar 255px/4rem pada kontainer dan 285px/3.5rem pada gap sama dengan aplikasi asli (ukur dengan DevTools).

---

## Fase 4 — Halaman Publik

| Halaman | Sumber | Catatan |
|---|---|---|
| Landing `index.html` | `features/landing` (±184 baris) | Grid `lg:grid-cols-[1.15fr_1fr]`, kiri `bg-zinc-950`, kanan `bg-black` |
| Dialog Sign In / Register | `features/landing/components/auth-dialog.tsx`, `features/auth` (±492 baris) | Satu dialog, dua mode |
| Halaman `auth/login`, `auth/register` | `routes/auth/*` | Tambahkan pembatas percobaan login (`use-login-throttle.ts`) |

Tombol Google OAuth: tempatkan sebagai placeholder sampai Fase 9.

---

## Fase 5 — Halaman Ringan (≤ 300 baris)

1. `about` — `features/about`
2. `home` — `features/home` (247 baris)
3. `reports` (indeks) — `features/reports/reports-page` (270 baris)
4. `dashboard` — `routes/_authenticated/dashboard.tsx`

---

## Fase 6 — Halaman Menengah (300–700 baris)

1. `reports/general-ledgers` (permanent, temporary) — 351 baris
2. `reports/worksheet` — 393 baris
3. `reports/trial-balances` (unadjusted, adjusted, post-closing) — 505 baris
4. `tools` — 652 baris

Pola yang berulang di fase ini: kartu "tanpa periode", tabel laporan, format angka rupiah (`_lib/format.ts`). Jadikan satu fungsi JS bersama di `assets/js/format.js`.

---

## Fase 7 — Halaman Berat (700–1100 baris)

1. `journal-entry` — 751 baris (form jurnal, tabel baris, validasi debit = kredit)
2. `reports/journals` (general, adjusting, closing) — 856 baris
3. `chart-of-accounts` — 859 baris (tabel + dialog tambah/ubah/hapus)
4. `periods` — 913 baris (form buka periode, tabel periode)
5. `reports/financial-statements` — 1.101 baris (laba rugi, posisi keuangan, arus kas, laba ditahan)

Validasi bawaan memakai Zod dan react-hook-form. Padanan murni HTML5: atribut `required`, `pattern`, `min`, `max`, ditambah fungsi validasi JS kecil.

---

## Fase 8 — Pengaturan (Terbesar)

`settings` — 1.366 baris: tab Account, Security, Appearance. Kerjakan per tab, satu tab satu komit.

---

## Fase 9 — Integrasi dan Kontrol Kualitas

1. **Router:** semua tautan antarhalaman memakai file `.html` (atau atur ulang `404.html` untuk GitHub Pages).
2. **API:** ganti data contoh dengan `fetch` ke backend (`aumo-finance/backend`). Letakkan di `assets/js/api.js`. Referensi kontrak: `src/features/*/api/*.ts` dan `openapi-config.ts`.
3. **Grafik:** komponen dasbor memakai Recharts. Padanan murni: SVG manual atau Chart.js via `<script>`.
4. **Ekspor Excel:** pustaka `xlsx` dapat dimuat lewat CDN.
5. **Uji visual:** jalankan aplikasi asli dan versi HTML5 pada ukuran layar sama (1440, 1024, 768, 390 px; mode gelap dan terang). Bandingkan tangkapan layar dengan Playwright (`toHaveScreenshot`) sampai selisih piksel nol.
6. **Deploy:** aktifkan GitHub Pages dari folder `/docs`.

---

## Template Tugas per Komponen/Halaman

Salin untuk setiap item:

```
[ ] Buka file sumber .tsx, catat semua string className
[ ] Salin ke HTML, ganti {kondisi} menjadi atribut/data-state
[ ] Sisipkan ikon SVG inline
[ ] Tulis JS perilaku (jika ada)
[ ] Uji mode gelap + terang
[ ] Uji 4 lebar layar
[ ] Bandingkan dengan aplikasi asli (berdampingan)
[ ] Komit: "feat(fase-X): <nama komponen/halaman>"
```

## Aturan Komit

Satu komponen atau satu halaman = satu komit. Format: `feat(fase-3): sidebar`, `fix(fase-2): jarak badge`.

## Urutan Ringkas

`Fase 0 → 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9`
