
<script>
  import { categories } from '#lib/data/stories.js';

  // Svelte 5 bindable properties
  let {
    activeCategory = $bindable('For You'),
    searchQuery = $bindable('')
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

<header class="site-header">
  <div class="header-main">

    <!-- Yahoo Logo -->
    <a href="/" class="logo" aria-label="Yahoo Home">
      yahoo<span>!</span>
    </a>

    <!-- Search Bar -->
    <div class="search-container">
      <input
        type="search"
        placeholder="Search news and topics..."
        aria-label="Search news and topics"
        bind:value={searchQuery}
      />

      <button
        type="button"
        class="search-button"
        onclick={() => {}}
      >
        Search
      </button>
    </div>

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

      <span class="profile-avatar">P</span>
    </div>

  </div>

  <!-- Navigation -->
  <nav class="navigation" aria-label="News categories">
    {#each categories as category}
      <button
        type="button"
        class:active={activeCategory === category}
        onclick={() => {
          activeCategory = category;
        }}
      >
        {category}
      </button>
    {/each}
  </nav>
</header>
