<script lang="ts">
  import Navbar from '$lib/components/Navbar.svelte';
  import Footer from '$lib/components/Footer.svelte';
  import '../app.css';

  let { children } = $props();
</script>

<div class="grain min-h-screen bg-[#0a0a0a] text-[#f5f1e8]">
  <Navbar />
  <main>
    {@render children()}
  </main>
  <Footer />
</div>
