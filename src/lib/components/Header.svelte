
<script>
  import { categories } from '#lib/data/stories.js';

  // Svelte 5 bindable properties
  let {
    activeCategory = $bindable('For You'),
    searchQuery = $bindable(''),
    // Landing view: page shows its own large logo and search
    minimal = false,
    onHome = () => {}
  } = $props();

  let darkMode = $state(false);

  function toggleTheme() {
    darkMode = !darkMode;

    document.documentElement.classList.toggle(
      'dark-mode',
      darkMode
    );
  }
</script>

<header class="site-header" class:minimal>
  <div class="header-main">

    <!-- Yahoo Logo -->
    {#if !minimal}
    <a href="/" class="logo" aria-label="Yahoo Home" onclick={onHome}>
      yahoo<span>!</span>
    </a>

    <!-- Search Bar -->
    <form
      class="search-container"
      role="search"
      onsubmit={(event) => event.preventDefault()}
    >
      <input
        type="search"
        placeholder="Search news and topics..."
        aria-label="Search news and topics"
        bind:value={searchQuery}
      />

      <button type="submit" class="search-button">
        Search
      </button>
    </form>
    {/if}

    <!-- Header Actions -->
    <div class="header-actions">

      <button
        type="button"
        class="theme-button"
        onclick={toggleTheme}
        aria-label="Toggle dark mode"
      >
        {darkMode ? '☀️' : '🌙'}
      </button>

      <span class="mail-link">✉ Mail</span>

      <span class="profile-avatar" role="img" aria-label="Profile">M</span>
    </div>

  </div>

  <!-- Navigation -->
  <nav class="navigation" aria-label="News categories">
    {#each categories as category}
      {#if category === 'Finance'}
        <a href="/finance" class="finance-nav-link">
          Finance
        </a>
      {:else}
        <button
          type="button"
          class:active={activeCategory === category}
          aria-pressed={activeCategory === category}
          onclick={() => {
            activeCategory = category;
          }}
        >
          {category}
        </button>
      {/if}
    {/each}
  </nav>
</header>

<style>
  .finance-nav-link {
    display: inline-flex;
    align-items: center;
    color: #666274;
    font-size: 13px;
    font-weight: 600;
    padding: 13px 0;
    border-bottom: 3px solid transparent;
    white-space: nowrap;
  }

  .finance-nav-link:hover {
    color: #6001d2;
    border-bottom-color: #6001d2;
  }
</style>
