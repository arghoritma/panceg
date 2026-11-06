<script lang="ts">
  import { Link } from '@inertiajs/svelte'
  import Brand from '../components/Brand.svelte'
  import { session } from '../session'

  const { user, loading } = $derived($session)
</script>

<svelte:head><title>Panceg</title></svelte:head>

<main class="min-h-screen overflow-hidden bg-bg text-text">
  <div class="mx-auto max-w-[1240px] px-5 py-5 max-md:px-4">
    <header class="flex items-center justify-between gap-4 rounded-2xl border border-border bg-surface/90 px-5 py-3 shadow-card backdrop-blur max-sm:px-4">
      <Brand href="/" class="text-lg" />
      {#if !loading}
        <div class="flex items-center gap-2">
          {#if user}
            <Link href="/dashboard" class="btn btn-primary">Open workspace</Link>
          {:else}
            <Link href="/login" class="hidden px-3 py-2 text-sm font-semibold text-text hover:text-primary hover:no-underline sm:inline-flex">Sign in</Link>
            <Link href="/register" class="btn btn-primary">Start free</Link>
          {/if}
        </div>
      {/if}
    </header>

    <section class="grid min-h-[650px] grid-cols-[1.08fr_.92fr] items-center gap-12 py-14 max-lg:grid-cols-1 max-lg:gap-8 max-sm:py-10">
      <div class="max-w-[650px]">
        <p class="mb-5 inline-flex items-center gap-2 rounded-full border border-border bg-surface px-3 py-1.5 text-xs font-bold uppercase tracking-[0.16em] text-primary">
          A steadier starting point
        </p>
        <h1 class="m-0 max-w-[620px] text-[clamp(3.1rem,7vw,6.1rem)] font-bold leading-[0.94] tracking-[-0.065em]">
          Build the part that matters.
        </h1>
        <p class="mt-7 max-w-[540px] text-lg leading-8 text-muted max-sm:text-base max-sm:leading-7">
          Panceg gives your next product a considered foundation: secure accounts,
          a clean application shell, server rendering, and room to make it yours.
        </p>
        <div class="mt-9 flex flex-wrap items-center gap-3">
          {#if loading}
            <span class="h-11 w-32 animate-pulse rounded-lg bg-primary-soft" aria-label="Loading"></span>
          {:else if user}
            <Link href="/dashboard" class="btn btn-primary px-5 py-3">Go to dashboard</Link>
          {:else}
            <Link href="/register" class="btn btn-primary px-5 py-3">Create your account</Link>
            <Link href="/login" class="btn btn-ghost px-5 py-3">I already have an account</Link>
          {/if}
        </div>
        <div class="mt-12 flex flex-wrap gap-x-7 gap-y-3 text-sm text-muted">
          <span>Authentication included</span>
          <span>SQLite by default</span>
          <span>SSR ready</span>
        </div>
      </div>

      <div class="relative mx-auto w-full max-w-[490px]">
        <div class="absolute -inset-8 -z-10 rounded-[40px] bg-primary-soft/75 blur-2xl"></div>
        <div class="rounded-[28px] border border-border bg-surface p-4 shadow-card max-sm:p-3">
          <div class="rounded-[20px] bg-[#332719] p-6 text-[#fff8ea] max-sm:p-5">
            <div class="flex items-center justify-between text-xs font-semibold uppercase tracking-[0.14em] text-[#f4cc83]">
              <span>Workspace</span>
              <span>Today</span>
            </div>
            <p class="mb-2 mt-12 text-3xl font-bold tracking-tight">A clear place to start.</p>
            <p class="m-0 max-w-[310px] text-sm leading-6 text-[#dbc7a7]">Your foundation handles the baseline, so every new screen can be deliberate.</p>
            <div class="mt-8 grid grid-cols-2 gap-3">
              <div class="rounded-2xl bg-white/10 p-4">
                <p class="m-0 text-xs text-[#dbc7a7]">Built for</p>
                <p class="mb-0 mt-2 text-lg font-bold">Momentum</p>
              </div>
              <div class="rounded-2xl bg-[#e0a644] p-4 text-[#332719]">
                <p class="m-0 text-xs font-semibold">Your stack</p>
                <p class="mb-0 mt-2 text-lg font-bold">Your rules</p>
              </div>
            </div>
          </div>
          <div class="mt-4 grid grid-cols-[1fr_auto] items-center gap-4 rounded-[20px] bg-bg px-5 py-4">
            <div>
              <p class="m-0 text-sm font-bold">Designed to adapt</p>
              <p class="mb-0 mt-1 text-xs text-muted">A calm base for product work.</p>
            </div>
            <span class="h-10 w-10 rounded-full bg-primary-soft"></span>
          </div>
        </div>
      </div>
    </section>
  </div>
</main>
