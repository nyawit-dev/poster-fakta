<script lang="ts">
  import { onMount } from 'svelte';
  import CopyButton from '$lib/components/CopyButton.svelte';
  import news from '$lib/data/news.json';

  // Hangatkan font sejak halaman dimuat, supaya download sinkron
  // langsung pakai font yang benar tanpa perlu await saat klik.
  onMount(() => {
    document.fonts?.ready.then(() => {}).catch(() => {});
  });

  type Pattern = 'none' | 'stripes' | 'dots' | 'grid' | 'rings';
  type Theme = {
    id: string; label: string;
    bg: string; panel: string; accent: string; accent2: string;
    text: string; sub: string; pattern: Pattern;
  };
  const themes: Theme[] = [
    { id: 'protes', label: 'Protes Gelap', bg: '#0a0a0a', panel: '#141414', accent: '#e11d48', accent2: '#f59e0b', text: '#f5f1e8', sub: '#a3a3a3', pattern: 'none' },
    { id: 'kertas', label: 'Kertas Koran', bg: '#f5f1e8', panel: '#ffffff', accent: '#dc2626', accent2: '#0a0a0a', text: '#111827', sub: '#4b5563', pattern: 'grid' },
    { id: 'hijau', label: 'Hijau Rakyat', bg: '#052e16', panel: '#14532d', accent: '#22c55e', accent2: '#facc15', text: '#f0fdf4', sub: '#bbf7d0', pattern: 'none' },
    { id: 'kuning', label: 'Brutalist Kuning', bg: '#facc15', panel: '#111111', accent: '#facc15', accent2: '#e11d48', text: '#ffffff', sub: '#d4d4d4', pattern: 'stripes' },
    { id: 'neon', label: 'Neon Cyber', bg: '#020617', panel: '#0f172a', accent: '#22d3ee', accent2: '#e879f9', text: '#e2e8f0', sub: '#94a3b8', pattern: 'grid' },
    { id: 'laut', label: 'Biru Laut', bg: '#082f49', panel: '#0c4a6e', accent: '#38bdf8', accent2: '#7dd3fc', text: '#f0f9ff', sub: '#bae6fd', pattern: 'rings' },
    { id: 'senja', label: 'Senja Bara', bg: '#431407', panel: '#7c2d12', accent: '#fb923c', accent2: '#fde68a', text: '#fff7ed', sub: '#fed7aa', pattern: 'dots' },
    { id: 'minimal', label: 'Minimal Putih', bg: '#ffffff', panel: '#ffffff', accent: '#111111', accent2: '#e11d48', text: '#111111', sub: '#6b7280', pattern: 'none' },
    { id: 'revolusi', label: 'Merah Revolusi', bg: '#450a0a', panel: '#7f1d1d', accent: '#fca5a5', accent2: '#facc15', text: '#fef2f2', sub: '#fecaca', pattern: 'stripes' },
    { id: 'ungu', label: 'Ungu Senja', bg: '#2e1065', panel: '#4c1d95', accent: '#c084fc', accent2: '#f472b6', text: '#faf5ff', sub: '#ddd6fe', pattern: 'dots' }
  ];

  type Layout = { id: string; label: string };
  const layouts: Layout[] = [
    { id: 'klasik', label: 'Klasik' },
    { id: 'raksasa', label: 'Headline Raksasa' },
    { id: 'quote', label: 'Kutipan Satir' }
  ];

  type Size = { id: string; label: string; w: number; h: number };
  const sizes: Size[] = [
    { id: 'square', label: 'Persegi 1080×1080', w: 1080, h: 1080 },
    { id: 'portrait', label: 'Potret 1080×1350', w: 1080, h: 1350 },
    { id: 'story', label: 'Story 1080×1920', w: 1080, h: 1920 }
  ];

  const KATEGORI = ['GERAKAN', 'STRATEGI', 'EDUKASI', 'ANGKA', 'PANDUAN', 'FAKTA', 'KARHUTLA', 'KEBIJAKAN', 'BANJIR', 'HARGA', 'HOAKS', 'UMUM'];

  let tema: Theme = themes[0];
  let layout = 'klasik';
  let ukuran: Size = sizes[0];
  let judul = '#CuekinBuzzer — Belajar dari Brasil';
  let ringkasan = 'Windah Basudara menyerukan gerakan #CuekinBuzzer: berhenti memberi engagement ke buzzer dan mengalihkan dukungan ke konten kepentingan publik.';
  let sumber = 'Instagram @windahbasudara';
  let tanggal = '2026-11-05';
  let kategori = 'GERAKAN';
  let punchline = 'Dari Brasil kita belajar: buzzer dikalahkan bukan dengan teriakan, tapi dengan cuek berjamaah.';
  let pesan = '';

  function loadPreset(n: (typeof news)[number]) {
    judul = n.judul;
    ringkasan = n.ringkasan;
    sumber = n.sumber;
    tanggal = n.tanggal;
    kategori = (n as any).kategori ?? 'UMUM';
    punchline = (n as any).punchline ?? punchline;
    pesan = `Preset dimuat: ${n.judul}`;
  }

  function makeCaption() {
    return `${judul}\n\n${ringkasan}\n\nSumber: ${sumber} (${tanggal}) · Kategori: ${kategori}\n\n${punchline}\n#CuekinBuzzer`;
  }

  function wrap(ctx: CanvasRenderingContext2D, text: string, maxWidth: number): string[] {
    const words = text.split(/\s+/).filter(Boolean);
    const lines: string[] = [];
    let line = '';
    for (const w of words) {
      const test = line ? line + ' ' + w : w;
      if (ctx.measureText(test).width > maxWidth && line) {
        lines.push(line);
        line = w;
      } else line = test;
    }
    if (line) lines.push(line);
    return lines;
  }

  function contrast(hex: string): string {
    const n = parseInt(hex.slice(1), 16);
    const r = (n >> 16) & 255, g = (n >> 8) & 255, b = n & 255;
    return (r * 299 + g * 587 + b * 114) / 1000 > 160 ? '#111111' : '#ffffff';
  }

  function star(ctx: CanvasRenderingContext2D, cx: number, cy: number, spikes: number, outer: number, inner: number) {
    let rot = (Math.PI / 2) * 3;
    const step = Math.PI / spikes;
    ctx.beginPath();
    ctx.moveTo(cx, cy - outer);
    for (let i = 0; i < spikes; i++) {
      ctx.lineTo(cx + Math.cos(rot) * outer, cy + Math.sin(rot) * outer);
      rot += step;
      ctx.lineTo(cx + Math.cos(rot) * inner, cy + Math.sin(rot) * inner);
      rot += step;
    }
    ctx.lineTo(cx, cy - outer);
    ctx.closePath();
  }

  function drawPattern(ctx: CanvasRenderingContext2D, t: Theme, W: number, H: number) {
    ctx.save();
    if (t.pattern === 'stripes') {
      ctx.globalAlpha = 0.16;
      ctx.fillStyle = t.accent;
      for (let x = -H; x < W + H; x += 56) {
        ctx.beginPath();
        ctx.moveTo(x, H); ctx.lineTo(x + H, 0); ctx.lineTo(x + H + 20, 0); ctx.lineTo(x + 20, H);
        ctx.closePath(); ctx.fill();
      }
    } else if (t.pattern === 'dots') {
      ctx.globalAlpha = 0.25;
      ctx.fillStyle = t.accent;
      for (let x = 24; x < W; x += 48)
        for (let y = 24; y < H; y += 48) {
          ctx.beginPath(); ctx.arc(x, y, 6, 0, Math.PI * 2); ctx.fill();
        }
    } else if (t.pattern === 'grid') {
      ctx.globalAlpha = 0.14;
      ctx.strokeStyle = t.accent;
      ctx.lineWidth = 2;
      for (let x = 0; x < W; x += 48) { ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, H); ctx.stroke(); }
      for (let y = 0; y < H; y += 48) { ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(W, y); ctx.stroke(); }
    } else if (t.pattern === 'rings') {
      ctx.globalAlpha = 0.14;
      ctx.strokeStyle = t.accent;
      ctx.lineWidth = 3;
      for (let r = 80; r < W; r += 90) {
        ctx.beginPath(); ctx.arc(W, H, r, 0, Math.PI * 2); ctx.stroke();
      }
    }
    ctx.restore();
  }

  /** Art treatment khas tiap tema — di atas panel, di bawah teks */
  function drawPanelArt(ctx: CanvasRenderingContext2D, t: Theme, M: number, W: number, H: number) {
    const px = M, py = M, pw = W - M * 2, ph = H - M * 2;
    ctx.save();
    switch (t.id) {
      case 'protes': {
        ctx.globalAlpha = 0.9;
        ctx.fillStyle = t.accent;
        ctx.save();
        ctx.translate(px + pw * 0.55, py + ph);
        ctx.rotate(-0.35);
        ctx.fillRect(0, -46, pw * 0.55, 46);
        ctx.restore();
        ctx.globalAlpha = 0.85;
        ctx.fillStyle = t.accent2;
        ctx.beginPath(); ctx.arc(px + pw - 90, py + 120, 34, 0, Math.PI * 2); ctx.fill();
        break;
      }
      case 'kertas': {
        ctx.globalAlpha = 0.10;
        ctx.strokeStyle = t.accent;
        ctx.lineWidth = 2;
        for (let y = py + 90; y < py + ph; y += 64) {
          ctx.beginPath(); ctx.moveTo(px + 24, y); ctx.lineTo(px + pw - 24, y); ctx.stroke();
        }
        ctx.globalAlpha = 0.55;
        ctx.lineWidth = 5;
        ctx.beginPath(); ctx.moveTo(px + 24, py + 64); ctx.lineTo(px + pw - 24, py + 64); ctx.stroke();
        break;
      }
      case 'hijau': {
        ctx.globalAlpha = 0.22;
        ctx.fillStyle = t.accent;
        ctx.beginPath(); ctx.arc(px + pw - 120, py + 150, 150, 0, Math.PI * 2); ctx.fill();
        ctx.fillStyle = t.accent2;
        ctx.beginPath(); ctx.arc(px + pw - 240, py + 90, 70, 0, Math.PI * 2); ctx.fill();
        ctx.beginPath(); ctx.arc(px + 110, py + ph - 120, 90, 0, Math.PI * 2); ctx.fill();
        break;
      }
      case 'kuning': {
        ctx.globalAlpha = 0.95;
        ctx.fillStyle = t.accent;
        star(ctx, px + pw - 130, py + 140, 12, 110, 52);
        ctx.fill();
        ctx.globalAlpha = 0.35;
        ctx.fillStyle = t.accent2;
        ctx.fillRect(px + 40, py + ph - 70, pw - 80, 22);
        break;
      }
      case 'neon': {
        ctx.globalAlpha = 0.5;
        ctx.strokeStyle = t.accent;
        ctx.lineWidth = 4;
        ctx.shadowColor = t.accent; ctx.shadowBlur = 24;
        ctx.beginPath(); ctx.arc(px + pw - 150, py + 170, 100, 0, Math.PI * 2); ctx.stroke();
        ctx.strokeStyle = t.accent2; ctx.shadowColor = t.accent2;
        ctx.beginPath(); ctx.arc(px + pw - 190, py + 220, 160, 0, Math.PI * 2); ctx.stroke();
        ctx.shadowBlur = 0;
        break;
      }
      case 'laut': {
        ctx.globalAlpha = 0.22;
        ctx.strokeStyle = t.accent;
        ctx.lineWidth = 8;
        for (let row = 0; row < 4; row++) {
          const baseY = py + ph - 60 - row * 44;
          for (let x = px; x < px + pw; x += 90) {
            ctx.beginPath(); ctx.arc(x + 45, baseY, 45, Math.PI, 0); ctx.stroke();
          }
        }
        break;
      }
      case 'senja': {
        const sx = px + pw - 190, sy = py + 230;
        const g = ctx.createRadialGradient(sx, sy, 10, sx, sy, 210);
        g.addColorStop(0, t.accent2);
        g.addColorStop(0.55, t.accent);
        g.addColorStop(1, 'rgba(0,0,0,0)');
        ctx.globalAlpha = 0.85;
        ctx.fillStyle = g;
        ctx.beginPath(); ctx.arc(sx, sy, 210, 0, Math.PI * 2); ctx.fill();
        break;
      }
      case 'minimal': {
        ctx.globalAlpha = 0.8;
        ctx.strokeStyle = t.accent;
        ctx.lineWidth = 3;
        ctx.beginPath(); ctx.moveTo(px + 48, py + 84); ctx.lineTo(px + 240, py + 84); ctx.stroke();
        ctx.fillStyle = t.accent2;
        ctx.fillRect(px + 48, py + 100, 14, 14);
        break;
      }
      case 'revolusi': {
        ctx.globalAlpha = 0.28;
        ctx.fillStyle = t.accent;
        star(ctx, px + pw - 170, py + ph - 220, 5, 190, 80);
        ctx.fill();
        ctx.globalAlpha = 0.85;
        ctx.fillStyle = t.accent2;
        ctx.save();
        ctx.translate(px + pw / 2, py + 120);
        ctx.rotate(-0.06);
        ctx.fillRect(-pw / 2 + 60, -26, pw - 120, 52);
        ctx.restore();
        break;
      }
      case 'ungu': {
        ctx.globalAlpha = 0.2;
        ctx.fillStyle = t.accent;
        ctx.beginPath(); ctx.ellipse(px + pw - 150, py + 180, 190, 120, 0.5, 0, Math.PI * 2); ctx.fill();
        ctx.fillStyle = t.accent2;
        ctx.beginPath(); ctx.ellipse(px + 140, py + ph - 190, 170, 100, -0.4, 0, Math.PI * 2); ctx.fill();
        ctx.beginPath(); ctx.ellipse(px + pw - 300, py + ph - 140, 120, 70, 0.3, 0, Math.PI * 2); ctx.fill();
        break;
      }
    }
    ctx.restore();
  }

  function drawPoster(): HTMLCanvasElement {
    const W = ukuran.w, H = ukuran.h;
    const c = document.createElement('canvas');
    c.width = W; c.height = H;
    const ctx = c.getContext('2d')!;
    const t = tema;
    const M = 64;
    const pad = 48;
    const X = M + pad;
    const maxW = W - M * 2 - pad * 2;

    ctx.fillStyle = t.bg; ctx.fillRect(0, 0, W, H);
    drawPattern(ctx, t, W, H);

    // bayangan brutalist miring di belakang panel
    ctx.save();
    ctx.globalAlpha = 0.9;
    ctx.fillStyle = t.accent;
    ctx.fillRect(M + 18, M + 18, W - M * 2, H - M * 2);
    ctx.restore();

    ctx.fillStyle = t.panel; ctx.fillRect(M, M, W - M * 2, H - M * 2);
    ctx.strokeStyle = '#000000'; ctx.lineWidth = 10; ctx.strokeRect(M, M, W - M * 2, H - M * 2);
    ctx.fillStyle = t.accent; ctx.fillRect(M, M, W - M * 2, 20);

    drawPanelArt(ctx, t, M, W, H);

    // watermark raksasa miring
    ctx.save();
    ctx.translate(W * 0.58, H * 0.52);
    ctx.rotate(-0.16);
    ctx.globalAlpha = 0.07;
    ctx.fillStyle = t.text;
    ctx.font = '900 230px "Plus Jakarta Sans", sans-serif';
    ctx.textAlign = 'center';
    ctx.fillText('#CUEKIN', 0, 0);
    ctx.restore();
    ctx.textAlign = 'left';

    // header
    ctx.fillStyle = t.accent;
    ctx.font = '700 36px "JetBrains Mono", monospace';
    ctx.fillText('#CUEKINBUZZER', X, M + pad + 44);
    ctx.fillStyle = t.sub;
    ctx.font = '600 28px "JetBrains Mono", monospace';
    ctx.fillText(`${tanggal} · ${sumber}`, X, M + pad + 92);

    // stempel kategori berputar
    ctx.save();
    ctx.translate(W - M - pad - 110, M + pad + 52);
    ctx.rotate(-0.12);
    ctx.font = '800 26px "JetBrains Mono", monospace';
    const sw = ctx.measureText(kategori).width + 40;
    ctx.strokeStyle = t.accent; ctx.lineWidth = 5;
    ctx.strokeRect(-sw / 2, -30, sw, 56);
    ctx.fillStyle = t.accent;
    ctx.textAlign = 'center';
    ctx.fillText(kategori, 0, 10);
    ctx.restore();
    ctx.textAlign = 'left';

    const center = layout !== 'klasik';
    const TX = center ? W / 2 : X;
    ctx.textAlign = center ? 'center' : 'left';

    const footerY = H - M - pad - 40;
    const yTop = M + pad + 230;
    const budget = footerY - 60 - yTop; // ruang vertikal tersedia untuk semua teks

    // --- Adaptif: kecilkan font bertahap sampai semua teks muat dalam budget.
    // Ini yang mencegah teks bertumpuk untuk judul/ringkasan yang panjang.
    let titleSize = layout === 'raksasa' ? 108 : layout === 'quote' ? 58 : 72;
    let sumSize = layout === 'raksasa' ? 32 : 38;
    let punchSize = layout === 'quote' ? 80 : layout === 'raksasa' ? 50 : 40;
    let titleLines: string[] = [], sumLines: string[] = [], punchLines: string[] = [];

    for (let iter = 0; iter < 60; iter++) {
      ctx.font = `900 ${titleSize}px "Plus Jakarta Sans", sans-serif`;
      titleLines = wrap(ctx, judul, maxW);
      ctx.font = `600 ${sumSize}px "Plus Jakarta Sans", sans-serif`;
      sumLines = wrap(ctx, ringkasan, maxW);
      ctx.font = `800 ${punchSize}px "Plus Jakarta Sans", sans-serif`;
      punchLines = wrap(ctx, punchline, maxW);

      const quoteH = layout === 'quote' ? 150 : 0;
      const total =
        quoteH +
        titleLines.length * (titleSize + 14) +
        24 +
        sumLines.length * (sumSize + 12) +
        56 +
        punchLines.length * (punchSize + 12);

      if (total <= budget) break;
      if (titleSize <= 38 && sumSize <= 22 && punchSize <= 24) break;
      titleSize = Math.max(38, titleSize - 4);
      sumSize = Math.max(22, sumSize - 2);
      punchSize = Math.max(24, punchSize - 2);
    }

    let y = yTop;
    if (layout === 'quote') {
      ctx.fillStyle = t.accent;
      ctx.font = `900 ${Math.round(titleSize * 2.2)}px "Plus Jakarta Sans", sans-serif`;
      ctx.fillText('“', TX, y + 30);
      y += 150;
    }

    ctx.fillStyle = t.text;
    ctx.font = `900 ${titleSize}px "Plus Jakarta Sans", sans-serif`;
    for (const line of titleLines) {
      ctx.fillText(line, TX, y);
      y += titleSize + 14;
    }

    y += 24;
    ctx.fillStyle = t.sub;
    ctx.font = `600 ${sumSize}px "Plus Jakarta Sans", sans-serif`;
    for (const line of sumLines) {
      ctx.fillText(line, TX, y);
      y += sumSize + 12;
    }

    y += 56;
    ctx.fillStyle = t.accent;
    ctx.font = `800 ${punchSize}px "Plus Jakarta Sans", sans-serif`;
    let first = true;
    for (const line of punchLines) {
      ctx.fillText(line, TX, y);
      if (first) {
        const lw = ctx.measureText(line).width;
        const sx2 = center ? TX - lw / 2 : TX;
        ctx.globalAlpha = 0.85;
        ctx.fillRect(sx2, y + 12, lw, 7);
        ctx.globalAlpha = 1;
        first = false;
      }
      y += punchSize + 12;
    }

    ctx.textAlign = 'left';
    ctx.fillStyle = t.sub;
    ctx.font = '700 28px "JetBrains Mono", monospace';
    ctx.fillText('poster-fakta · nyawit-dev · MIT', X, footerY);
    return c;
  }

  // PENTING: download & buka tab HARUS sinkron dalam handler klik.
  // `await` sebelum klik akan menghilangkan user-gesture -> browser memblokir
  // download (saveBlob) maupun popup (window.open). Jadi gambar langsung di sini.
  function downloadPNG() {
    try {
      const c = drawPoster();
      const url = c.toDataURL('image/png');
      const a = document.createElement('a');
      a.href = url;
      a.download = `poster-fakta-${ukuran.id}.png`;
      document.body.appendChild(a);
      a.click();
      a.remove();
      pesan = `Poster ${ukuran.w}×${ukuran.h} berhasil diunduh. Kalau browser-mu memblokir, pakai tombol “Buka di Tab Baru”.`;
    } catch (e) {
      pesan = 'Gagal membuat poster. Coba refresh halaman lalu ulangi.';
    }
  }

  function openPNG() {
    // Buka jendela DULU (sinkron, saat gesture klik masih ada), isi belakangan.
    let win: Window | null = null;
    try {
      win = window.open('about:blank', '_blank');
    } catch {
      win = null;
    }
    try {
      const c = drawPoster();
      const url = c.toDataURL('image/png');
      if (win) {
        win.location.href = url;
        pesan = 'PNG dibuka di tab baru — klik kanan / tekan lama untuk simpan.';
      } else {
        // popup diblokir -> fallback download langsung
        const a = document.createElement('a');
        a.href = url;
        a.download = `poster-fakta-${ukuran.id}.png`;
        document.body.appendChild(a);
        a.click();
        a.remove();
        pesan = 'Popup diblokir browser — file diunduh langsung sebagai gantinya.';
      }
    } catch (e) {
      if (win) win.close();
      pesan = 'Gagal membuat poster. Coba refresh halaman lalu ulangi.';
    }
  }

  function patternCSS(t: Theme): string {
    if (t.pattern === 'stripes') return `repeating-linear-gradient(45deg, ${t.accent} 0 8px, transparent 8px 36px)`;
    if (t.pattern === 'dots') return `radial-gradient(circle, ${t.accent} 2.5px, transparent 3px)`;
    if (t.pattern === 'grid') return `linear-gradient(${t.accent} 1px, transparent 1px), linear-gradient(90deg, ${t.accent} 1px, transparent 1px)`;
    if (t.pattern === 'rings') return `radial-gradient(circle at 85% 85%, transparent 46px, ${t.accent} 47px 49px, transparent 50px)`;
    return 'none';
  }
</script>

<section class="mx-auto max-w-6xl px-4 pt-14 pb-10">
  <p class="inline-block rounded-full border-2 border-black bg-[#22c55e] px-3 py-1 font-mono text-xs font-black text-black">TOOL · SATIR · #CUEKINBUZZER</p>
  <h1 class="mt-6 text-5xl font-black leading-[0.95] tracking-tight md:text-7xl">
    POSTER <span class="text-[#e11d48]">FAKTA</span><br />
    <span class="outline-text">SIAP SHARE.</span>
  </h1>
  <p class="mt-5 max-w-2xl text-lg font-semibold text-white/85">
    Pilih berita, atur teks, pilih tema, download poster PNG — tanpa server, tanpa login.
    Semua diproses di browser kamu. Yang provokatif dilewati, yang bermanfaat dibagikan.
  </p>
</section>

<section id="generator" class="mx-auto grid max-w-6xl gap-8 px-4 pb-14 lg:grid-cols-2">
  <!-- FORM -->
  <div class="card-shadow rounded-3xl border-2 border-black bg-[#f5f1e8] p-6 text-black md:p-8">
    <h2 class="text-2xl font-black">1 · Isi poster</h2>

    <label class="mt-6 block font-mono text-xs font-black tracking-widest" for="judul">JUDUL</label>
    <textarea id="judul" bind:value={judul} rows="2" class="mt-2 w-full rounded-xl border-2 border-black bg-white p-3 font-bold" maxlength="160"></textarea>

    <label class="mt-4 block font-mono text-xs font-black tracking-widest" for="ringkasan">RINGKASAN</label>
    <textarea id="ringkasan" bind:value={ringkasan} rows="3" class="mt-2 w-full rounded-xl border-2 border-black bg-white p-3 font-semibold" maxlength="320"></textarea>

    <div class="mt-4 grid grid-cols-2 gap-3">
      <div>
        <label class="block font-mono text-xs font-black tracking-widest" for="sumber">SUMBER</label>
        <input id="sumber" bind:value={sumber} class="mt-2 w-full rounded-xl border-2 border-black bg-white p-3 font-semibold" />
      </div>
      <div>
        <label class="block font-mono text-xs font-black tracking-widest" for="tanggal">TANGGAL</label>
        <input id="tanggal" bind:value={tanggal} class="mt-2 w-full rounded-xl border-2 border-black bg-white p-3 font-semibold" />
      </div>
    </div>

    <div class="mt-4 grid grid-cols-2 gap-3">
      <div>
        <label class="block font-mono text-xs font-black tracking-widest" for="kategori">KATEGORI</label>
        <select id="kategori" bind:value={kategori} class="mt-2 w-full rounded-xl border-2 border-black bg-white p-3 font-bold">
          {#each KATEGORI as k}<option value={k}>{k}</option>{/each}
        </select>
      </div>
      <div>
        <label class="block font-mono text-xs font-black tracking-widest" for="punchline">PUNCHLINE SATIR</label>
        <input id="punchline" bind:value={punchline} class="mt-2 w-full rounded-xl border-2 border-black bg-white p-3 font-semibold" maxlength="150" />
      </div>
    </div>

    <h2 class="mt-8 text-2xl font-black">2 · Tema, layout & ukuran</h2>
    <p class="mt-3 font-mono text-xs font-black tracking-widest">TEMA (10) — tiap tema punya gaya gambar sendiri</p>
    <div class="mt-2 flex flex-wrap gap-2">
      {#each themes as t}
        <button onclick={() => (tema = t)} class="rounded-full border-2 border-black px-3 py-1.5 text-xs font-black transition {tema.id === t.id ? 'bg-black text-white' : 'bg-white text-black hover:-translate-y-0.5'}">{t.label}</button>
      {/each}
    </div>
    <p class="mt-4 font-mono text-xs font-black tracking-widest">LAYOUT</p>
    <div class="mt-2 flex flex-wrap gap-2">
      {#each layouts as l}
        <button onclick={() => (layout = l.id)} class="rounded-full border-2 border-black px-4 py-1.5 text-xs font-black transition {layout === l.id ? 'bg-[#e11d48] text-white' : 'bg-white text-black hover:-translate-y-0.5'}">{l.label}</button>
      {/each}
    </div>
    <p class="mt-4 font-mono text-xs font-black tracking-widest">UKURAN</p>
    <div class="mt-2 flex flex-wrap gap-2">
      {#each sizes as s}
        <button onclick={() => (ukuran = s)} class="rounded-full border-2 border-black px-4 py-1.5 text-xs font-black transition {ukuran.id === s.id ? 'bg-[#22c55e] text-black' : 'bg-white text-black hover:-translate-y-0.5'}">{s.label}</button>
      {/each}
    </div>

    <h2 class="mt-8 text-2xl font-black">3 · Unduh & share</h2>
    <div class="mt-4 flex flex-wrap items-center gap-3">
      <button onclick={downloadPNG} class="card-shadow-sm rounded-full border-2 border-black bg-[#e11d48] px-6 py-3 font-black text-white transition hover:-translate-y-0.5">⬇ Download PNG</button>
      <button onclick={openPNG} class="rounded-full border-2 border-black bg-white px-5 py-3 font-black text-black transition hover:-translate-y-0.5">Buka di Tab Baru</button>
      <CopyButton text={makeCaption()} label="Salin Caption" />
    </div>
    {#if pesan}<p class="mt-3 font-mono text-sm font-black text-[#15803d]">{pesan}</p>{/if}
    <p class="mt-4 text-xs font-semibold text-black/60">Poster digambar langsung di browser (Canvas). Tidak ada data yang dikirim ke server. Kalau download tidak jalan di browser-mu, pakai “Buka di Tab Baru” lalu simpan manual.</p>
  </div>

  <!-- PREVIEW -->
  <div>
    <div class="card-shadow relative mx-auto w-full max-w-[520px] overflow-hidden rounded-3xl border-2 border-black" style="background:{tema.bg}; color:{tema.text}; aspect-ratio: {ukuran.w} / {ukuran.h}">
      <div class="absolute inset-0 opacity-20" style="background-image:{patternCSS(tema)}; background-size: {tema.pattern === 'dots' ? '28px 28px' : tema.pattern === 'rings' ? '130px 130px' : '48px 48px'}"></div>
      <div class="pointer-events-none absolute -right-8 -top-8 h-32 w-32 rounded-full opacity-30" style="background:{tema.accent}"></div>
      <div class="pointer-events-none absolute left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 -rotate-12 select-none whitespace-nowrap text-[86px] font-black leading-none opacity-[0.08]">#CUEKIN</div>
      <div class="absolute inset-4 rounded-2xl border-[3px] border-black p-5 md:inset-6 md:p-7" style="background:{tema.panel}">
        <div style="height:6px; background:{tema.accent}" class="-m-5 mb-5 md:-m-7 md:mb-7"></div>
        <div class="flex flex-wrap items-start justify-between gap-2">
          <div>
            <p class="font-mono text-xs font-black tracking-widest" style="color:{tema.accent}">#CUEKINBUZZER</p>
            <p class="mt-1 font-mono text-[11px] font-bold" style="color:{tema.sub}">{tanggal} · {sumber}</p>
          </div>
          <span class="-rotate-6 rounded-sm border-[3px] px-2 py-1 font-mono text-[10px] font-black" style="border-color:{tema.accent}; color:{tema.accent}">{kategori}</span>
        </div>
        {#if layout === 'quote'}<p class="mt-4 text-6xl font-black leading-none" style="color:{tema.accent}">“</p>{/if}
        <h3 class="line-clamp-4 {layout === 'raksasa' ? 'text-4xl md:text-5xl' : layout === 'quote' ? 'text-2xl md:text-3xl' : 'text-3xl md:text-4xl'} mt-3 font-black leading-tight">{judul}</h3>
        <p class="line-clamp-5 mt-3 font-semibold leading-relaxed" style="color:{tema.sub}">{ringkasan}</p>
        <p class="line-clamp-3 {layout === 'quote' ? 'text-3xl md:text-4xl' : 'text-xl md:text-2xl'} mt-5 font-black leading-snug" style="color:{tema.accent}">{layout === 'quote' ? `“${punchline}”` : punchline}</p>
        <p class="absolute bottom-4 left-5 font-mono text-[10px] font-bold md:bottom-6 md:left-7" style="color:{tema.sub}">poster-fakta · nyawit-dev · MIT</p>
      </div>
    </div>
    <p class="mt-3 text-center font-mono text-xs text-white/60">Preview live ({ukuran.label}) — PNG hasil unduhan mengikuti tema, layout, dan ukuran ini.</p>
  </div>
</section>

<section id="preset" class="border-y-4 border-black bg-[#f5f1e8] text-black">
  <div class="mx-auto max-w-6xl px-4 py-14">
    <h2 class="text-4xl font-black">Preset berita #CuekinBuzzer</h2>
    <p class="mt-2 font-semibold text-black/70">Klik satu berita untuk memuat judul, ringkasan, kategori, dan punchline satirnya ke generator. Semua dari pemberitaan tepercaya — baca sumber aslinya sebelum share.</p>
    <div class="mt-8 grid gap-4 md:grid-cols-2">
      {#each news as n}
        <button onclick={() => loadPreset(n)} class="card-shadow-sm rounded-2xl border-2 border-black bg-white p-5 text-left transition hover:-translate-y-0.5">
          <p class="font-mono text-xs font-black tracking-widest text-[#e11d48]">{n.tanggal} · {n.sumber.toUpperCase()} · {(n as any).kategori ?? ''}</p>
          <p class="mt-2 font-black leading-snug">{n.judul}</p>
          <p class="mt-1 text-sm font-semibold text-black/65">{n.ringkasan}</p>
          <p class="mt-2 text-sm font-bold italic text-black/75">“{(n as any).punchline ?? ''}”</p>
          <p class="mt-3 font-mono text-xs font-black">MUAT KE GENERATOR →</p>
        </button>
      {/each}
    </div>
  </div>
</section>

<section id="cara" class="mx-auto max-w-6xl px-4 py-14">
  <h2 class="text-4xl font-black">Cara pakai</h2>
  <div class="mt-8 grid gap-4 md:grid-cols-3">
    <div class="card-shadow rounded-2xl border-2 border-black bg-[#22c55e] p-6 text-black">
      <p class="font-mono text-4xl font-black">01</p>
      <p class="mt-2 font-black">Pilih preset atau tulis sendiri</p>
      <p class="mt-1 text-sm font-semibold">Judul, ringkasan, kategori, sumber, tanggal, punchline — semua bisa diedit.</p>
    </div>
    <div class="card-shadow rounded-2xl border-2 border-black bg-[#f59e0b] p-6 text-black">
      <p class="font-mono text-4xl font-black">02</p>
      <p class="mt-2 font-black">Pilih tema, layout & ukuran</p>
      <p class="mt-1 text-sm font-semibold">10 tema bergaya beda (starburst, matahari senja, gelombang, glow neon, garis koran), 3 layout, 3 ukuran.</p>
    </div>
    <div class="card-shadow rounded-2xl border-2 border-black bg-[#e11d48] p-6 text-white">
      <p class="font-mono text-4xl font-black">03</p>
      <p class="mt-2 font-black">Download & bagikan</p>
      <p class="mt-1 text-sm font-semibold">PNG siap untuk Instagram, WhatsApp, X. Salin juga caption-nya.</p>
    </div>
  </div>
  <p class="mt-8 rounded-2xl border-2 border-black bg-white p-5 font-mono text-sm font-bold text-black">
    Disclaimer: poster dari tool ini adalah ringkasan edukasi independen. Selalu cantumkan sumber aslinya dan baca beritanya sebelum menyebarkan.
  </p>
</section>
