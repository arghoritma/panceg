<script lang="ts">
  import Brand from './Brand.svelte'

  let { children }: { children: import('svelte').Snippet } = $props()
</script>

<main class="min-h-screen bg-bg p-4 sm:p-6">
  <div class="mx-auto grid min-h-[calc(100vh-2rem)] max-w-[1180px] grid-cols-[.88fr_1.12fr] overflow-hidden rounded-[28px] border border-border bg-surface shadow-card max-lg:grid-cols-1 sm:min-h-[calc(100vh-3rem)]">
    <section class="relative flex min-h-[360px] flex-col justify-between overflow-hidden bg-[#332719] p-8 text-[#fff8ea] max-lg:min-h-0 max-lg:p-7 max-sm:p-6">
      <div class="absolute -right-24 -top-20 h-72 w-72 rounded-full bg-[#e0a644]/25 blur-3xl"></div>
      <Brand href="/" class="relative text-[#fff8ea] [&_svg]:text-[#e0a644]" />
      <div class="relative max-w-[350px] max-lg:mt-20">
        <p class="m-0 text-xs font-bold uppercase tracking-[0.18em] text-[#f4cc83]">Panceg foundation</p>
        <h2 class="mb-0 mt-4 text-[clamp(2rem,4vw,3.5rem)] font-bold leading-[0.98] tracking-[-0.05em]">Start with less noise.</h2>
        <p class="mb-0 mt-5 text-sm leading-6 text-[#dbc7a7]">A dependable base for the work you are ready to make your own.</p>
      </div>
      <p class="relative mb-0 mt-12 text-xs text-[#cdb58e] max-lg:hidden">Bun · Hono · Svelte · SQLite</p>
    </section>
    <section class="flex items-center justify-center p-8 max-sm:p-6">
      <div class="w-full max-w-[410px]">{@render children()}</div>
    </section>
  </div>
</main>
