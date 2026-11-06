<script lang="ts">
  import { router, useForm, usePage } from '@inertiajs/svelte'
  import Layout from '../components/Layout.svelte'
  import Field from '../components/Field.svelte'
  import type { ApiToken } from '../../shared/types'

  let {
    tokens,
    apiPort,
    createdToken,
  }: { tokens: ApiToken[]; apiPort: number; createdToken: string | null } = $props()

  const page = usePage()
  const user = $derived(page.props.auth.user)

  let form = $state(useForm({ name: '', expiresInDays: null as number | null }))
  let expiryChoice = $state('never')
  let copied = $state(false)

  // The raw token arrives only in the creation response, never in session flash.
  $effect(() => {
    if (createdToken) {
      form.reset()
      expiryChoice = 'never'
    }
  })

  type Status = 'active' | 'expired' | 'revoked'

  function statusOf(token: ApiToken): Status {
    if (token.revokedAt) return 'revoked'
    if (token.expiresAt && Date.parse(token.expiresAt) <= Date.now())
      return 'expired'
    return 'active'
  }

  function formatDateTime(iso: string | null): string {
    if (!iso) return '—'
    return new Date(iso).toLocaleString(undefined, {
      year: 'numeric',
      month: 'short',
      day: 'numeric',
      hour: '2-digit',
      minute: '2-digit',
    })
  }

  async function copyToken() {
    if (!createdToken) return
    try {
      await navigator.clipboard.writeText(createdToken)
      copied = true
      setTimeout(() => (copied = false), 2000)
    } catch {
      /* clipboard unavailable (permissions) — the token is selectable as text */
    }
  }

  function submit(e: SubmitEvent) {
    e.preventDefault()
    form.expiresInDays =
      expiryChoice === 'never' ? null : Number(expiryChoice)
    form.post('/tokens')
  }

  function revoke(token: ApiToken) {
    const ok = confirm(
      `Revoke "${token.name}"? Requests using it stop working immediately.`,
    )
    if (ok) router.post(`/tokens/${token.id}/revoke`)
  }

  const inputClass =
    'w-full px-4 py-3 border border-border rounded-xl bg-bg text-text text-[0.95rem] focus:outline-2 focus:outline-primary focus:-outline-offset-1 focus:border-primary'

  const btnPrimary =
    'inline-flex items-center justify-center gap-1.5 px-4 py-3 border border-primary rounded-xl bg-primary text-white font-semibold text-sm cursor-pointer transition-colors hover:bg-primary-hover hover:border-primary-hover hover:no-underline disabled:opacity-60 disabled:cursor-not-allowed'

  const STATUS_STYLES: Record<Status, string> = {
    active:
      'bg-green-50 text-green-700 border-green-200 dark:bg-green-950 dark:text-green-300 dark:border-green-800',
    expired:
      'bg-amber-50 text-amber-700 border-amber-200 dark:bg-amber-950 dark:text-amber-300 dark:border-amber-800',
    revoked:
      'bg-red-50 text-red-700 border-red-200 dark:bg-red-950 dark:text-red-300 dark:border-red-800',
  }
</script>

<svelte:head><title>API tokens</title></svelte:head>

{#if user}
  <Layout>
    <section class="grid grid-cols-[1fr_auto] items-end gap-6 max-sm:grid-cols-1">
      <div class="max-w-[650px]">
        <p class="m-0 text-xs font-bold uppercase tracking-[0.16em] text-primary">Developer access</p>
        <h1 class="mb-0 mt-3 text-[clamp(2rem,4vw,3rem)] font-bold leading-none tracking-[-0.045em]">Keys for your integrations.</h1>
        <p class="mb-0 mt-4 leading-7 text-muted">Create one personal bearer token per integration. The standalone API listens on port {apiPort}.</p>
      </div>
      <span class="rounded-full border border-border bg-surface px-3 py-1.5 text-xs font-bold text-muted">{tokens.length} token{tokens.length === 1 ? '' : 's'}</span>
    </section>

    {#if createdToken}
      <section
        class="mt-7 mb-5 p-5 rounded-[20px] border border-green-200 bg-green-50 dark:bg-green-950 dark:border-green-800"
        aria-label="New API token"
      >
        <p class="m-0 mb-2 text-sm font-semibold text-green-700 dark:text-green-300">
          Copy your new token now — closing this page is permanent.
        </p>
        <div class="flex items-center gap-2 flex-wrap">
          <code
            class="flex-1 min-w-0 px-3 py-2 rounded-lg bg-bg border border-border font-mono text-sm break-all select-all"
          >
            {createdToken}
          </code>
          <button
            type="button"
            class={btnPrimary}
            onclick={copyToken}
          >
            {copied ? 'Copied' : 'Copy'}
          </button>
        </div>
        <p class="m-0 mt-2 text-xs text-green-700 dark:text-green-300">
          Send it as <code class="font-mono">Authorization: Bearer
            {createdToken.slice(0, 12)}…</code> on every API request.
        </p>
      </section>
    {/if}

    <section class="mt-8 bg-[#332719] text-[#fff8ea] rounded-[24px] p-7 shadow-card max-sm:p-5 mb-8">
      <div class="mb-6">
        <p class="m-0 text-xs font-bold uppercase tracking-[0.14em] text-[#f4cc83]">New integration</p>
        <h2 class="text-xl m-0 mt-2">Create a token</h2>
        <p class="m-0 mt-1 text-sm text-[#dbc7a7]">Name it for the tool that will use it, then choose its lifetime.</p>
      </div>
      <form onsubmit={submit} novalidate>
        <div class="grid grid-cols-[1fr_200px_auto] gap-4 items-end max-md:grid-cols-1">
          <Field id="token-name" label="Name" error={form.errors.name}>
            <input
              id="token-name"
              type="text"
              name="name"
              maxlength="60"
              placeholder="e.g. Mobile app, CI pipeline"
              class={inputClass}
              bind:value={form.name}
              onchange={() => form.clearErrors('name')}
            />
          </Field>
          <Field
            id="token-expiry"
            label="Expiration"
            error={form.errors.expiresInDays}
          >
            <select
              id="token-expiry"
              name="expiresInDays"
              class={inputClass}
              bind:value={expiryChoice}
            >
              <option value="never">Never expires</option>
              <option value="7">7 days</option>
              <option value="30">30 days</option>
              <option value="90">90 days</option>
              <option value="365">1 year</option>
            </select>
          </Field>
          <div class="mb-4 max-md:mb-0">
            <button class={btnPrimary} type="submit" disabled={form.processing}>
              {form.processing ? 'Creating…' : 'Create token'}
            </button>
          </div>
        </div>
      </form>
    </section>

    <section class="overflow-hidden rounded-[24px] border border-border bg-surface shadow-card">
      <div class="flex items-center justify-between gap-4 px-7 py-5 max-sm:px-5">
        <div>
          <p class="m-0 text-xs font-bold uppercase tracking-[0.14em] text-muted">Access inventory</p>
          <h2 class="text-xl m-0 mt-1">Your tokens</h2>
        </div>
        <span class="rounded-full bg-primary-soft px-3 py-1 text-xs font-bold text-primary">Review regularly</span>
      </div>
      {#if tokens.length === 0}
        <p class="border-t border-border px-7 py-8 text-muted text-sm m-0 max-sm:px-5">
          No tokens yet. Create one above to start calling the API.
        </p>
      {:else}
        <div class="overflow-x-auto border-t border-border">
          <table class="w-full border-collapse text-sm">
            <thead>
              <tr>
              <th class="text-left px-6 py-3 border-b border-border whitespace-nowrap text-muted text-xs font-bold uppercase tracking-[0.12em] bg-bg">
                  Name
                </th>
              <th class="text-left px-6 py-3 border-b border-border whitespace-nowrap text-muted text-xs font-bold uppercase tracking-[0.12em] bg-bg">
                  Status
                </th>
              <th class="text-left px-6 py-3 border-b border-border whitespace-nowrap text-muted text-xs font-bold uppercase tracking-[0.12em] bg-bg">
                  Created
                </th>
              <th class="text-left px-6 py-3 border-b border-border whitespace-nowrap text-muted text-xs font-bold uppercase tracking-[0.12em] bg-bg">
                  Expires
                </th>
              <th class="text-left px-6 py-3 border-b border-border whitespace-nowrap text-muted text-xs font-bold uppercase tracking-[0.12em] bg-bg">
                  Last used
                </th>
              <th class="text-right px-6 py-3 border-b border-border whitespace-nowrap text-muted text-xs font-bold uppercase tracking-[0.12em] bg-bg">
                  <span class="sr-only">Actions</span>
                </th>
              </tr>
            </thead>
            <tbody class="[&>tr:last-child>td]:border-b-0">
              {#each tokens as token (token.id)}
                {@const status = statusOf(token)}
                <tr class="transition-colors hover:bg-primary-soft/70">
                  <td class="text-left px-6 py-4 border-b border-border whitespace-nowrap">
                    <span class="font-semibold block">{token.name}</span>
                    <code class="text-xs text-muted font-mono">
                      {token.tokenPrefix}…
                    </code>
                  </td>
                  <td class="text-left px-6 py-4 border-b border-border whitespace-nowrap">
                    <span
                      class="inline-block px-2 py-0.5 rounded-full text-xs font-semibold border {STATUS_STYLES[status]}"
                    >
                      {status}
                    </span>
                  </td>
                  <td class="text-left px-6 py-4 border-b border-border whitespace-nowrap text-muted">
                    {formatDateTime(token.createdAt)}
                  </td>
                  <td class="text-left px-6 py-4 border-b border-border whitespace-nowrap text-muted">
                    {formatDateTime(token.expiresAt)}
                  </td>
                  <td class="text-left px-6 py-4 border-b border-border whitespace-nowrap text-muted">
                    {formatDateTime(token.lastUsedAt)}
                  </td>
                  <td class="text-right px-6 py-4 border-b border-border whitespace-nowrap">
                    {#if status !== 'revoked'}
                      <button
                        type="button"
                        class="px-3 py-2 border border-border rounded-xl bg-transparent text-danger text-xs font-semibold cursor-pointer transition-colors hover:bg-primary-soft hover:border-red-300 disabled:opacity-60 disabled:cursor-not-allowed"
                        onclick={() => revoke(token)}
                      >
                        Revoke
                      </button>
                    {/if}
                  </td>
                </tr>
              {/each}
            </tbody>
          </table>
        </div>
      {/if}
    </section>
  </Layout>
{/if}
