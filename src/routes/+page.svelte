<script lang="ts">
  import CopyButton from '$lib/components/CopyButton.svelte';
  import news from '$lib/data/news.json';

  type Theme = {
    id: string; label: string;
    bg: string; panel: string; accent: string; text: string; sub: string;
  };
  const themes: Theme[] = [
    { id: 'protes', label: 'Protes Gelap', bg: '#0a0a0a', panel: '#111111', accent: '#e11d48', text: '#f5f1e8', sub: '#a3a3a3' },
    { id: 'kertas', label: 'Kertas Koran', bg: '#f5f1e8', panel: '#ffffff', accent: '#e11d48', text: '#0a0a0a', sub: '#525252' },
    { id: 'hijau', label: 'Hijau Rakyat', bg: '#052e16', panel: '#14532d', accent: '#22c55e', text: '#f0fdf4', sub: '#bbf7d0' }
  ];

  let tema: Theme = themes[0];
  let judul = '#CuekinBuzzer — Belajar dari Brasil';
  let ringkasan = 'Windah Basudara menyerukan gerakan #CuekinBuzzer: berhenti memberi engagement ke buzzer dan mengalihkan dukungan ke konten kepentingan publik.';
  let sumber = 'Instagram @windahbasudara';
  let tanggal = '2026-11-05';
  let punchline = 'Diam bukan berarti buta. Cuek bukan berarti setuju.';
  let pesan = '';
  let canvas: HTMLCanvasElement;

  function loadPreset(n: (typeof news)[number]) {
    judul = n.judul;
    ringkasan = n.ringkasan;
    sumber = n.sumber;
    tanggal = n.tanggal;
    pesan = `Preset dimuat: ${n.judul}`;
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

  async function downloadPNG() {
    await document.fonts.ready;
    const S = 1080;
    const c = document.createElement('canvas');
    c.width = S; c.height = S;
    const ctx = c.getContext('2d')!;
    const t = tema;

    ctx.fillStyle = t.bg; ctx.fillRect(0, 0, S, S);
    ctx.fillStyle = t.panel; ctx.fillRect(48, 48, S - 96, S - 96);
    ctx.strokeStyle = '#000'; ctx.lineWidth = 10; ctx.strokeRect(48, 48, S - 96, S - 96);
    ctx.fillStyle = t.accent; ctx.fillRect(48, 48, S - 96, 18);

    ctx.fillStyle = t.accent;
    ctx.font = '700 34px "JetBrains Mono", monospace';
    ctx.fillText('#CUEKINBUZZER', 96, 150);
    ctx.fillStyle = t.sub;
    ctx.font = '600 28px "JetBrains Mono", monospace';
    ctx.fillText(`${tanggal}  ·  ${sumber}`, 96, 196);

    ctx.fillStyle = t.text;
    ctx.font = '900 76px "Plus Jakarta Sans", sans-serif';
    let y = 310;
    for (const line of wrap(ctx, judul, S - 192)) {
      ctx.fillText(line, 96, y);
      y += 92;
    }

    y += 28;
    ctx.fillStyle = t.sub;
    ctx.font = '600 38px "Plus Jakarta Sans", sans-serif';
    for (const line of wrap(ctx, ringkasan, S - 192)) {
      ctx.fillText(line, 96, y);
      y += 52;
    }

    ctx.fillStyle = t.accent;
    ctx.font = '800 40px "Plus Jakarta Sans", sans-serif';
    y = Math.min(y + 64, S - 220);
    for (const line of wrap(ctx, punchline, S - 192)) {
      ctx.fillText(line, 96, y);
      y += 54;
    }

    ctx.fillStyle = t.sub;
    ctx.font = '700 30px "JetBrains Mono", monospace';
    ctx.fillText('poster-fakta · nyawit-dev · MIT', 96, S - 110);

    c.toBlob((blob) => {
      if (!blob) return;
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = 'poster-fakta-cuekinbuzzer.png';
      a.click();
      URL.revokeObjectURL(url);
      pesan = 'Poster PNG 1080×1080 berhasil diunduh.';
    }, 'image/png');
  }

  $: caption = `${judul}\n\n${ringkasan}\n\nSumber: ${sumber} (${tanggal})\n\n${punchline}\n#CuekinBuzzer`;
</script>

<section class="mx-auto max-w-6xl px-4 pt-14 pb-10">
  <p class="inline-block rounded-full border-2 border-black bg-[#22c55e] px-3 py-1 font-mono text-xs font-black text-black">TOOL · SATIR · #CUEKINBUZZER</p>
  <h1 class="mt-6 text-5xl font-black leading-[0.95] tracking-tight md:text-7xl">
    POSTER <span class="text-[#e11d48]">FAKTA</span><br />
    <span class="outline-text">SIAP SHARE.</span>
  </h1>
  <p class="mt-5 max-w-2xl text-lg font-semibold text-white/85">
    Pilih berita, atur teks, download poster PNG 1080×1080 — tanpa server, tanpa login.
    Semua diproses di browser kamu. Yang provokatif dilewati, yang bermanfaat dibagikan.
  </p>
</section>

<section id="generator" class="mx-auto grid max-w-6xl gap-8 px-4 pb-14 lg:grid-cols-2">
  <!-- FORM -->
  <div class="card-shadow rounded-3xl border-2 border-black bg-[#f5f1e8] p-6 text-black md:p-8">
    <h2 class="text-2xl font-black">1 · Isi poster</h2>

    <label class="mt-6 block font-mono text-xs font-black tracking-widest" for="judul">JUDUL</label>
    <textarea id="judul" bind:value={judul} rows="2" class="mt-2 w-full rounded-xl border-2 border-black bg-white p-3 font-bold" maxlength="120"></textarea>

    <label class="mt-4 block font-mono text-xs font-black tracking-widest" for="ringkasan">RINGKASAN</label>
    <textarea id="ringkasan" bind:value={ringkasan} rows="3" class="mt-2 w-full rounded-xl border-2 border-black bg-white p-3 font-semibold" maxlength="220"></textarea>

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

    <label class="mt-4 block font-mono text-xs font-black tracking-widest" for="punchline">PUNCHLINE SATIR</label>
    <input id="punchline" bind:value={punchline} class="mt-2 w-full rounded-xl border-2 border-black bg-white p-3 font-semibold" maxlength="100" />

    <h2 class="mt-8 text-2xl font-black">2 · Pilih tema</h2>
    <div class="mt-4 flex flex-wrap gap-2">
      {#each themes as t}
        <button
          on:click={() => (tema = t)}
          class="rounded-full border-2 border-black px-4 py-2 text-sm font-black transition {tema.id === t.id ? 'bg-black text-white' : 'bg-white text-black hover:-translate-y-0.5'}"
        >{t.label}</button>
      {/each}
    </div>

    <h2 class="mt-8 text-2xl font-black">3 · Unduh & share</h2>
    <div class="mt-4 flex flex-wrap items-center gap-3">
      <button on:click={downloadPNG} class="card-shadow-sm rounded-full border-2 border-black bg-[#e11d48] px-6 py-3 font-black text-white transition hover:-translate-y-0.5">⬇ Download PNG</button>
      <CopyButton text={caption} label="Salin Caption" />
    </div>
    {#if pesan}<p class="mt-3 font-mono text-sm font-black text-[#15803d]">{pesan}</p>{/if}
    <p class="mt-4 text-xs font-semibold text-black/60">Poster digambar langsung di browser (Canvas 1080×1080). Tidak ada data yang dikirim ke server.</p>
  </div>

  <!-- PREVIEW -->
  <div>
    <div class="card-shadow overflow-hidden rounded-3xl border-2 border-black" style="background:{tema.bg}; color:{tema.text}">
      <div class="m-6 rounded-2xl border-2 border-black p-6 md:p-8" style="background:{tema.panel}">
        <div style="height:8px; background:{tema.accent}" class="-m-6 mb-6 md:-m-8 md:mb-8"></div>
        <p class="font-mono text-sm font-black tracking-widest" style="color:{tema.accent}">#CUEKINBUZZER</p>
        <p class="mt-1 font-mono text-xs font-bold" style="color:{tema.sub}">{tanggal} · {sumber}</p>
        <h3 class="mt-6 text-3xl font-black leading-tight md:text-4xl">{judul}</h3>
        <p class="mt-4 font-semibold leading-relaxed" style="color:{tema.sub}">{ringkasan}</p>
        <p class="mt-6 text-xl font-black" style="color:{tema.accent}">{punchline}</p>
        <p class="mt-8 font-mono text-xs font-bold" style="color:{tema.sub}">poster-fakta · nyawit-dev · MIT</p>
      </div>
    </div>
    <p class="mt-3 text-center font-mono text-xs text-white/60">Preview live — hasil PNG 1080×1080 akan sama seperti ini.</p>
    <canvas bind:this={canvas} width="1080" height="1080" class="hidden"></canvas>
  </div>
</section>

<section id="preset" class="border-y-4 border-black bg-[#f5f1e8] text-black">
  <div class="mx-auto max-w-6xl px-4 py-14">
    <h2 class="text-4xl font-black">Preset berita #CuekinBuzzer</h2>
    <p class="mt-2 font-semibold text-black/70">Klik satu berita untuk memuatnya ke generator. Semua dari pemberitaan tepercaya — baca sumber aslinya sebelum share.</p>
    <div class="mt-8 grid gap-4 md:grid-cols-2">
      {#each news as n}
        <button on:click={() => loadPreset(n)} class="card-shadow-sm rounded-2xl border-2 border-black bg-white p-5 text-left transition hover:-translate-y-0.5">
          <p class="font-mono text-xs font-black tracking-widest text-[#e11d48]">{n.tanggal} · {n.sumber.toUpperCase()}</p>
          <p class="mt-2 font-black leading-snug">{n.judul}</p>
          <p class="mt-1 text-sm font-semibold text-black/65">{n.ringkasan}</p>
          <p class="mt-3 font-mono text-xs font-black">MuAT KE GENERATOR →</p>
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
      <p class="mt-1 text-sm font-semibold">Judul, ringkasan, sumber, tanggal — semua bisa diedit.</p>
    </div>
    <div class="card-shadow rounded-2xl border-2 border-black bg-[#f59e0b] p-6 text-black">
      <p class="font-mono text-4xl font-black">02</p>
      <p class="mt-2 font-black">Pilih tema & punchline</p>
      <p class="mt-1 text-sm font-semibold">Tiga tema poster-protes. Punchline boleh diganti dengan satir versimu.</p>
    </div>
    <div class="card-shadow rounded-2xl border-2 border-black bg-[#e11d48] p-6 text-white">
      <p class="font-mono text-4xl font-black">03</p>
      <p class="mt-2 font-black">Download & bagikan</p>
      <p class="mt-1 text-sm font-semibold">PNG 1080×1080 siap untuk Instagram, WhatsApp, X. Salin juga caption-nya.</p>
    </div>
  </div>
  <p class="mt-8 rounded-2xl border-2 border-black bg-white p-5 font-mono text-sm font-bold text-black">
    Disclaimer: poster dari tool ini adalah ringkasan edukasi independen. Selalu cantumkan sumber aslinya dan baca beritanya sebelum menyebarkan.
  </p>
</section>
