# Poster Fakta #CuekinBuzzer

> Diam bukan berarti buta. Cuek bukan berarti setuju.

Generator poster fakta 1080×1080 siap share untuk gerakan #CuekinBuzzer Indonesia — pilih berita, atur teks & tema, download PNG. 100% di browser, tanpa backend.

**Live:** https://nyawit-dev.github.io/poster-fakta/

## Fitur

- Form editor: judul, ringkasan, sumber, tanggal, punchline satir
- 3 tema: Protes Gelap, Kertas Koran, Hijau Rakyat
- Preview live + export Canvas PNG 1080×1080
- Preset 8 berita #CuekinBuzzer dari pemberitaan tepercaya (Kompas, Katadata, Suara.com, Akurat, Mojok, KOMBI.ID, IDN Times, RRI)
- Salin caption siap share (WA / Instagram / X / Threads)

## Tech

- SvelteKit (Svelte 5) + TypeScript + TailwindCSS v4
- `@sveltejs/adapter-static` → GitHub Pages via GitHub Actions (`.github/workflows/deploy.yml`)
- Tanpa database, tanpa tracking

## Jalankan lokal

```bash
npm install
npm run dev     # http://localhost:5173
npm run build   # output ke build/
```

## Deploy

Push ke `main` → Actions build (`BASE_PATH=/poster-fakta`) → upload artifact → GitHub Pages.
Settings repo: **Pages → Source: GitHub Actions**.

## Lisensi

MIT.
