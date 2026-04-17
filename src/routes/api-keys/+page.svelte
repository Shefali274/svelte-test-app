<script lang="ts">
 import '$lib/assets/styles/layout.css';
  import { writable } from 'svelte/store';

  type KeyStatus = 'active' | 'disabled' | 'expired';

  interface ApiKey {
    id: string;
    name: string;
    key: string;
    status: KeyStatus;
    expires: string | null;
    created: string;
    lastUsed: string | null;
  }

  const apiKeys = writable<ApiKey[]>([
    { id: '1', name: 'ai_inference_key', key: 'a1b2...x9yz', status: 'active',   expires: 'In 29 days', created: '03.04.2026', lastUsed: 'Never' },
    { id: '2', name: 'model_training_key', key: 'c3d4...w8vu', status: 'active', expires: 'In 27 days', created: '31.03.2026', lastUsed: '7 hours ago' },
    { id: '3', name: 'vision_model_key',   key: 'e5f6...t7rs',  status: 'expired',   expires: '-', created: '25.03.2026', lastUsed: '2 days ago' },
    { id: '4', name: 'vision_model_key_v1',key: 'g7h8...q6po', status: 'expired',  expires: '-', created: '05.03.2026', lastUsed: '13 days ago' },
  ]);

  let showCreateModal = false;
  let newKeyName = '';

    const openMenuId = writable<string | null>(null);


  function toggleMenu(id: string) {
  openMenuId.update(current => current === id ? null : id);
}

function closeMenus(event: MouseEvent) {
  const target = event.target as HTMLElement;
  if (!target.closest('.menu-wrap')) {
    openMenuId.set(null);
  }
}

function deleteKey(id: string) {
  apiKeys.update(keys => keys.filter(k => k.id !== id));
  openMenuId.set(null);
}

function toggleDisable(id: string) {
  apiKeys.update(keys =>
    keys.map(k =>
      k.id === id
        ? { ...k, status: k.status === 'disabled' ? 'active' : 'disabled' }
        : k
    )
  );
  openMenuId.set(null);
}

function isOpen(id: string): boolean {
  // No longer needed — use $openMenuId directly in template
  return false;
}

  function createKey() {
    if (!newKeyName.trim()) return;
    const newKey: ApiKey = {
      id: Date.now().toString(),
      name: newKeyName.trim(),
      key: 'sk-' + Math.random().toString(36).slice(2, 6) + '...' + Math.random().toString(36).slice(2, 6),
      status: 'active',
      expires: 'In 30 days',
      created: new Date().toLocaleDateString('en-GB').replace(/\//g, '.'),
      lastUsed: null,
    };
    apiKeys.update(keys => [newKey, ...keys]);
    newKeyName = '';
    showCreateModal = false;
  }


</script>
<svelte:window on:click={closeMenus} />

<div class="page">
  <!-- Header -->
  <div class="header-info">
        <div class="list-item">$145,20</div>
        <div class="user-info">RG</div>
        <h1 class="page-title page-title-ms">API keys</h1>
    </div>
    <div>
  <div class="page-header">
    <div>
      <h1 class="page-title page-title-desk">API keys</h1>
      <p class="page-desc">Manage your API keys to access all models</p>
    </div>
    <button class="btn-create" on:click={() => showCreateModal = true}>
      <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16" fill="none">
        <path d="M3.33325 8.00016H12.6666M7.99992 3.3335V12.6668" stroke="#FAFAFA" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
      </svg>
      Create API key
    </button>
    </div>
  </div>

  <!-- Desktop Table -->
  <div class="px-16">
  <div class="table-wrapper">
    <table class="api-table">
      <thead>
        <tr>
          <th>Name</th>
          <th>API key</th>
          <th>Status</th>
          <th>Expires</th>
          <th>Created</th>
          <th>Last used</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        {#each $apiKeys as key (key.id + openMenuId)}
          <tr class="table-row">
            <td class="name-cell">{key.name}</td>
            <td class="key-cell"><code>{key.key}</code></td>
            <td>
                <span class="badge badge-{key.status}">
                  {key.status.charAt(0).toUpperCase() + key.status.slice(1)}
                </span>
            </td>
            <td class="muted">{key.expires ?? '—'}</td>
            <td class="muted">{key.created}</td>
            <td class="muted">{key.lastUsed ?? 'Never'}</td>
            <td class="actions-cell">
              <div class="menu-wrap" on:click|stopPropagation>
                <button class="icon-btn" on:click={() => toggleMenu(key.id)}>
                 <svg xmlns="http://www.w3.org/2000/svg" width="3" height="13" viewBox="0 0 3 13" fill="none">
  <path d="M1.41667 6.75C1.78486 6.75 2.08333 6.45152 2.08333 6.08333C2.08333 5.71514 1.78486 5.41667 1.41667 5.41667C1.04848 5.41667 0.75 5.71514 0.75 6.08333C0.75 6.45152 1.04848 6.75 1.41667 6.75Z" stroke="#FAFAFA" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
  <path d="M1.41667 2.08333C1.78486 2.08333 2.08333 1.78486 2.08333 1.41667C2.08333 1.04848 1.78486 0.75 1.41667 0.75C1.04848 0.75 0.75 1.04848 0.75 1.41667C0.75 1.78486 1.04848 2.08333 1.41667 2.08333Z" stroke="#FAFAFA" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
  <path d="M1.41667 11.4167C1.78486 11.4167 2.08333 11.1182 2.08333 10.75C2.08333 10.3818 1.78486 10.0833 1.41667 10.0833C1.04848 10.0833 0.75 10.3818 0.75 10.75C0.75 11.1182 1.04848 11.4167 1.41667 11.4167Z" stroke="#FAFAFA" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
</svg>
                </button>
                {#if $openMenuId === key.id}
                    <div class="dropdown">
                        <button class="dropdown-item">Edit</button>
                        <button class="dropdown-item" on:click={() => toggleDisable(key.id)}>
                        {key.status === 'disabled' ? 'Enable' : 'Disable'}
                        </button>
                        <button class="dropdown-item danger" on:click={() => deleteKey(key.id)}>Delete</button>
                    </div>
                {/if}
              </div>
            </td>
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
  </div>
  <!-- Mobile Card List -->
  <div class="mobile-list">
    {#each $apiKeys as key (key.id + openMenuId)}
      <div class="key-card">
        <div class="card-main">
          <div class="card-info">
            <p class="card-name">{key.name} <span class="card-key">{key.key.slice(3, 12)}...{key.key.slice(-4)}</span></p>
            <p class="card-meta">
              {#if key.expires}
                Expires {key.expires}{key.lastUsed ? `, used ${key.lastUsed}` : ', never used'}
              {:else}
                {key.lastUsed ? `Used ${key.lastUsed}` : 'Never used'}
              {/if}
            </p>
          </div>
          <div class="card-right">
            <span class="badge badge-{key.status}">
            {key.status.charAt(0).toUpperCase() + key.status.slice(1)}
            </span>
            <div class="menu-wrap" on:click|stopPropagation>
              <button class="icon-btn" on:click={() => toggleMenu(key.id)}>
                <svg xmlns="http://www.w3.org/2000/svg" width="3" height="13" viewBox="0 0 3 13" fill="none">
                <path d="M1.41667 6.75C1.78486 6.75 2.08333 6.45152 2.08333 6.08333C2.08333 5.71514 1.78486 5.41667 1.41667 5.41667C1.04848 5.41667 0.75 5.71514 0.75 6.08333C0.75 6.45152 1.04848 6.75 1.41667 6.75Z" stroke="#FAFAFA" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
                <path d="M1.41667 2.08333C1.78486 2.08333 2.08333 1.78486 2.08333 1.41667C2.08333 1.04848 1.78486 0.75 1.41667 0.75C1.04848 0.75 0.75 1.04848 0.75 1.41667C0.75 1.78486 1.04848 2.08333 1.41667 2.08333Z" stroke="#FAFAFA" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
                <path d="M1.41667 11.4167C1.78486 11.4167 2.08333 11.1182 2.08333 10.75C2.08333 10.3818 1.78486 10.0833 1.41667 10.0833C1.04848 10.0833 0.75 10.3818 0.75 10.75C0.75 11.1182 1.04848 11.4167 1.41667 11.4167Z" stroke="#FAFAFA" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
                </svg>
              </button>
             {#if $openMenuId === key.id}
                <div class="dropdown dropdown-left">
                    <button class="dropdown-item">Edit</button>  <!-- add this -->
                    <button class="dropdown-item" on:click={() => toggleDisable(key.id)}>
                    {key.status === 'disabled' ? 'Enable' : 'Disable'}
                    </button>
                    <button class="dropdown-item danger" on:click={() => deleteKey(key.id)}>Delete</button>
                </div>
                {/if}
            </div>
          </div>
        </div>
      </div>
    {/each}
  </div>
</div>

<!-- Create Modal -->
{#if showCreateModal}
  <!-- svelte-ignore a11y-click-events-have-key-events -->
  <!-- svelte-ignore a11y-no-static-element-interactions -->
  <div class="modal-overlay" on:click={() => showCreateModal = false}>
    <div class="modal" on:click|stopPropagation>
      <h2 class="modal-title">Create API key</h2>
      <p class="modal-desc">Give your key a descriptive name</p>
      <input
        class="modal-input"
        type="text"
        placeholder="e.g. production_key"
        bind:value={newKeyName}
        on:keydown={(e) => e.key === 'Enter' && createKey()}
      />
      <div class="modal-actions">
        <button class="btn-ghost" on:click={() => showCreateModal = false}>Cancel</button>
        <button class="btn-create" on:click={createKey}>Create key</button>
      </div>
    </div>
  </div>
{/if}
