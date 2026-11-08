# html5 — Aumo Finance (HTML5 Murni)

Duplikasi antarmuka web [Aumo Finance](https://github.com/rdmmoonlight/aumo-finance/tree/main/frontend) menggunakan **HTML5, CSS, dan JavaScript murni** (tanpa React, tanpa framework saat berjalan). Target: tampilan 100% identik dengan `/frontend` aslinya.

## Identitas

| | |
|---|---|
| Nama | `html5` (Aumo Finance — HTML5) |
| Pemilik | [rdmmoonlight](https://github.com/rdmmoonlight) |
| Rujukan | [`aumo-finance/frontend`](https://github.com/rdmmoonlight/aumo-finance/tree/main/frontend) |
| Keluaran | Folder [`/docs`](./docs) |
| Tema | Dark matte (default), light tersedia |
| Font | Inter |
| Lisensi | ISC |

## Status

Dalam perencanaan. Seluruh tahapan kerja ada di [`docs/FASE-FASE.md`](./docs/FASE-FASE.md).

## Menjalankan

```bash
pnpm install
pnpm exec vite   # server pengembangan lokal
```

Setelah Fase 0 selesai, halaman statis dari `/docs` dapat dipublikasikan melalui GitHub Pages (Settings → Pages → `main` → `/docs`).

## Struktur

```
.
├── docs/            # Keluaran HTML5 + panduan fase
│   └── FASE-FASE.md
├── index.html
├── package.json
└── README.md
```

## Lisensi

ISC © rdmmoonlight
