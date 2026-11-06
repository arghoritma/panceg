<script lang="ts">
  import { Link, useForm } from '@inertiajs/svelte'
  import AuthLayout from '../components/AuthLayout.svelte'
  import Field from '../components/Field.svelte'

  let { googleEnabled = false }: { googleEnabled?: boolean } = $props()

  const form = useForm({ name: '', email: '', password: '' })

  function submit(e: SubmitEvent) {
    e.preventDefault()
    form.post('/register')
  }
</script>

<svelte:head><title>Register</title></svelte:head>

<AuthLayout>
  <p class="m-0 text-xs font-bold uppercase tracking-[0.16em] text-primary">Make it yours</p>
  <h1 class="mb-2 mt-3 text-[2rem] font-bold leading-none tracking-tight">Create your account.</h1>
  <p class="text-muted mb-7">Set up your space and begin with a dependable foundation.</p>

  {#if googleEnabled}
    <a
      class="inline-flex items-center justify-center gap-1.5 px-4 py-3 w-full border border-border rounded-xl bg-bg font-semibold text-sm cursor-pointer transition-colors hover:bg-primary-soft hover:no-underline"
      href="/auth/google"
    >
      Register with Google
    </a>
    <div class="flex items-center gap-3 text-muted text-xs my-5">
       <span class="flex-1 h-px bg-border"></span>
      or
       <span class="flex-1 h-px bg-border"></span>
    </div>
  {/if}

  <form onsubmit={submit} novalidate>
    <Field id="name" label="Name" error={form.errors.name}>
      <input
        id="name"
        type="text"
        name="name"
        autocomplete="name"
        class="w-full px-4 py-3 border border-border rounded-xl bg-bg text-text text-[0.95rem] focus:outline-2 focus:outline-primary focus:-outline-offset-1 focus:border-primary"
        bind:value={form.name}
        onchange={() => form.clearErrors('name')}
      />
    </Field>

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
        autocomplete="new-password"
        class="w-full px-4 py-3 border border-border rounded-xl bg-bg text-text text-[0.95rem] focus:outline-2 focus:outline-primary focus:-outline-offset-1 focus:border-primary"
        bind:value={form.password}
        onchange={() => form.clearErrors('password')}
      />
      <p class="text-xs text-muted mt-1">At least 8 characters.</p>
    </Field>

    <button
      class="inline-flex items-center justify-center gap-1.5 px-4 py-3 w-full border border-primary rounded-xl bg-primary text-white font-semibold text-sm cursor-pointer transition-colors hover:bg-primary-hover hover:border-primary-hover hover:no-underline disabled:opacity-60 disabled:cursor-not-allowed"
      type="submit"
      disabled={form.processing}
    >
      {form.processing ? 'Creating account…' : 'Create account'}
    </button>
  </form>

  <p class="mt-5 text-center text-muted text-sm">
    Already have an account? <Link href="/login">Log in</Link>
  </p>
</AuthLayout>
