<script lang="ts">
  import { Link, useForm } from '@inertiajs/svelte'
  import AuthLayout from '../components/AuthLayout.svelte'
  import Field from '../components/Field.svelte'

  let { googleEnabled = false, notice = null }: { googleEnabled?: boolean; notice?: string | null } = $props()

  const form = useForm({ email: '', password: '' })

  function submit(e: SubmitEvent) {
    e.preventDefault()
    form.post('/login')
  }
  
</script>

<svelte:head><title>Login</title></svelte:head>

<AuthLayout>
  <p class="m-0 text-xs font-bold uppercase tracking-[0.16em] text-primary">Welcome back</p>
  <h1 class="mb-2 mt-3 text-[2rem] font-bold leading-none tracking-tight">Continue your work.</h1>
  <p class="text-muted mb-7">Log in to return to your Panceg workspace.</p>

  {#if notice}
    <div
      class="px-4 py-3 rounded-xl text-sm mb-6 border border-green-200 bg-green-50 text-green-700 dark:bg-green-950 dark:text-green-300 dark:border-green-800"
      role="status"
    >
      {notice}
    </div>
  {/if}

  {#if googleEnabled}
    <a
      class="inline-flex items-center justify-center gap-1.5 px-4 py-3 w-full border border-border rounded-xl bg-bg font-semibold text-sm cursor-pointer transition-colors hover:bg-primary-soft hover:no-underline"
      href="/auth/google"
    >
      Log in with Google
    </a>
    <div class="flex items-center gap-3 text-muted text-xs my-5">
       <span class="flex-1 h-px bg-border"></span>
      or
       <span class="flex-1 h-px bg-border"></span>
    </div>
  {/if}

  <form onsubmit={submit} novalidate>
    <Field id="email" label="Email" error={form.errors.email}>
      <input
        id="email"
        type="email"
        name="email"
        autocomplete="email"
        class="w-full px-4 py-3 border border-border rounded-xl bg-bg text-text text-[0.95rem] focus:outline-2 focus:outline-primary focus:-outline-offset-1 focus:border-primary"
        bind:value={form.email}
        onchange={() => form.clearErrors('email')}
      />
    </Field>

    <Field id="password" label="Password" error={form.errors.password}>
      <input
        id="password"
        type="password"
        name="password"
        autocomplete="current-password"
        class="w-full px-4 py-3 border border-border rounded-xl bg-bg text-text text-[0.95rem] focus:outline-2 focus:outline-primary focus:-outline-offset-1 focus:border-primary"
        bind:value={form.password}
        onchange={() => form.clearErrors('password')}
      />
    </Field>

    <div class="flex justify-end -mt-1 mb-4">
      <Link href="/forgot-password" class="text-sm">Forgot your password?</Link>
    </div>

    <button
      class="inline-flex items-center justify-center gap-1.5 px-4 py-3 w-full border border-primary rounded-xl bg-primary text-white font-semibold text-sm cursor-pointer transition-colors hover:bg-primary-hover hover:border-primary-hover hover:no-underline disabled:opacity-60 disabled:cursor-not-allowed"
      type="submit"
      disabled={form.processing}
    >
      {form.processing ? 'Signing in…' : 'Sign in'}
    </button>
  </form>

  <p class="mt-5 text-center text-muted text-sm">
    No account yet? <Link href="/register">Create one</Link>
  </p>
</AuthLayout>
