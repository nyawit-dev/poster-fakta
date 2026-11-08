<script lang="ts">
  import { base } from '$app/paths';
  import { page } from '$app/stores';
  const links = [
    { href: '#generator', label: 'Generator' },
    { href: '#preset', label: 'Preset Berita' },
    { href: '#cara', label: 'Cara Pakai' }
  ];
</script>

<header class="sticky top-0 z-40 border-b-4 border-black bg-[#0a0a0a]/95 backdrop-blur">
  <div class="mx-auto flex max-w-6xl flex-wrap items-center justify-between gap-3 px-4 py-3">
    <a href="{base}/" class="font-mono text-lg font-black tracking-tight">
      <span class="text-[#e11d48]">#</span>CUEKIN<span class="text-[#22c55e]">BUZZER</span>
    </a>
    <nav class="flex flex-wrap gap-1" aria-label="Utama">
      {#each links as l}
        <a
          href={l.href}
          class="rounded-full px-3 py-1.5 text-sm font-bold transition hover:bg-[#f5f1e8] hover:text-black {$page.url.pathname === l.href ? 'bg-[#e11d48] text-white' : 'text-[#f5f1e8]'}"
        >{l.label}</a>
      {/each}
    </nav>
  </div>
</header>
