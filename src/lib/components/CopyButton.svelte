<script lang="ts">
  let { text, label = 'Salin' }: { text: string; label?: string } = $props();
  let state = 'idle';
  async function copy() {
    try {
      await navigator.clipboard.writeText(text);
      state = 'copied';
    } catch {
      state = 'error';
    }
    setTimeout(() => (state = 'idle'), 2000);
  }
</script>

<button
  onclick={copy}
  class="card-shadow-sm inline-flex items-center gap-2 rounded-full border-2 border-black bg-[#22c55e] px-4 py-2 text-sm font-black text-black transition hover:-translate-y-0.5"
>
  {state === 'copied' ? '✓ Tersalin!' : state === 'error' ? 'Gagal — salin manual' : label}
</button>
