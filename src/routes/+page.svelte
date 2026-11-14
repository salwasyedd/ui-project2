
<script>

import Header from '#lib/components/Header.svelte';
import StoryCard from '#lib/components/StoryCard.svelte';
import Sidebar from '#lib/components/Sidebar.svelte';

import { stories, trendingTopics } from '#lib/data/stories.js';


  // Homepage state
  let activeCategory = $state('For You');
  let searchQuery = $state('');
  let savedArticles = $state([]);
  let selectedStory = $state(null);
  let showSavedOnly = $state(false);

  // Filter stories using Svelte 5 reactivity
  let filteredStories = $derived(
    stories.filter((story) => {
      const matchesCategory =
        activeCategory === 'For You' ||
        story.category === activeCategory;

      const query = searchQuery.toLowerCase().trim();

      const matchesSearch =
        story.title.toLowerCase().includes(query) ||
        story.summary.toLowerCase().includes(query) ||
        story.category.toLowerCase().includes(query);

      const matchesSaved =
        !showSavedOnly || savedArticles.includes(story.id);

      return matchesCategory && matchesSearch && matchesSaved;
    })
  );

  // Lead with one featured story, except in the saved list
  let featuredStory = $derived(
    showSavedOnly ? null : (filteredStories[0] ?? null)
  );

  let gridStories = $derived(
    featuredStory ? filteredStories.slice(1) : filteredStories
  );

  // "For You" with no search or Saved filter is a clean,
  // search-focused landing view with no feed
  let isHome = $derived(
    activeCategory === 'For You' &&
      !searchQuery.trim() &&
      !showSavedOnly
  );

  // Landing search applies on submit, so the input isn't
  // replaced by results mid-typing
  let homeQuery = $state('');

  function submitHomeSearch(event) {
    event.preventDefault();
    const query = homeQuery.trim();
    if (query) {
      searchQuery = query;
      homeQuery = '';
    }
  }

  // Save or unsave an article
  function toggleSave(id) {
    if (savedArticles.includes(id)) {
      savedArticles = savedArticles.filter(
        (savedId) => savedId !== id
      );
    } else {
      savedArticles = [...savedArticles, id];
    }
  }

  // Open article preview
  function openStory(story) {
    selectedStory = story;
  }

  // Close article preview
  function closeStory() {
    selectedStory = null;
  }

  // Search when a trending topic is clicked
  function searchTopic(topic) {
    activeCategory = 'For You';
    showSavedOnly = false;
    searchQuery = topic;

    window.scrollTo({
      top: 0,
      behavior: 'smooth'
    });
  }

  // Reset filters
  function clearFilters() {
    searchQuery = '';
    activeCategory = 'For You';
    showSavedOnly = false;
  }
</script>

<svelte:head>
  <title>Yahoo Redesign | Your News, Simplified</title>
  <meta
    name="description"
    content="A modern, simplified Yahoo homepage redesign."
  />
</svelte:head>

<svelte:window
  onkeydown={(event) => {
    if (event.key === 'Escape' && selectedStory) {
      closeStory();
    }
  }}
/>

<!-- HEADER -->
<Header
  bind:activeCategory
  bind:searchQuery
  minimal={isHome}
  onHome={clearFilters}
/>

<!-- MAIN HOMEPAGE -->
<main class="page-container" class:home-container={isHome}>

  {#if isHome}

  <!-- SEARCH-FOCUSED LANDING -->
  <section class="home-hero" aria-label="Yahoo search">
    <h1 class="logo home-logo">yahoo<span>!</span></h1>

    <form
      class="search-container home-search"
      role="search"
      onsubmit={submitHomeSearch}
    >
      <input
        type="search"
        placeholder="Search news and topics..."
        aria-label="Search news and topics"
        bind:value={homeQuery}
      />

      <button type="submit" class="search-button">
        Search
      </button>
    </form>

    <div class="home-shortcuts">
      {#each trendingTopics as topic}
        <button
          type="button"
          class="home-chip"
          onclick={() => searchTopic(topic)}
        >
          {topic}
        </button>
      {/each}

      <button
        type="button"
        class="home-chip"
        onclick={() => {
          showSavedOnly = true;
        }}
      >
        ☆ Saved ({savedArticles.length})
      </button>
    </div>
  </section>

  {:else}

  <!-- MAIN CONTENT AND SIDEBAR -->
  <div class="content-layout">

    <!-- NEWS SECTION -->
    <section class="main-content">

      <div class="section-heading">
        <div>
          <h2>
            {showSavedOnly
              ? 'Saved Stories'
              : activeCategory === 'For You'
                ? 'Top Stories'
                : activeCategory}
          </h2>

          {#if searchQuery.trim()}
            <p>Results for “{searchQuery.trim()}”</p>
          {/if}
        </div>

        <button
          class="view-saved-button"
          class:selected={showSavedOnly}
          aria-pressed={showSavedOnly}
          onclick={() => {
            showSavedOnly = !showSavedOnly;
          }}
        >
          {showSavedOnly
            ? 'All Stories'
            : `☆ Saved (${savedArticles.length})`}
        </button>
      </div>

      <!-- ARTICLES -->
      {#if filteredStories.length > 0}

        <!-- FEATURED STORY -->
        {#if featuredStory}
          <div class="featured-container">
            <StoryCard
              story={featuredStory}
              featured={true}
              saved={savedArticles.includes(featuredStory.id)}
              onRead={openStory}
              onSave={toggleSave}
            />
          </div>
        {/if}

        <!-- NEWS GRID -->
        {#if gridStories.length > 0}
          <div class="stories-grid">
            {#each gridStories as story (story.id)}
              <StoryCard
                {story}
                saved={savedArticles.includes(story.id)}
                onRead={openStory}
                onSave={toggleSave}
              />
            {/each}
          </div>
        {/if}

      {:else}

        <!-- NO ARTICLES FOUND -->
        <div class="empty-state">

          <h3>
            {showSavedOnly && savedArticles.length === 0
              ? 'No saved articles yet'
              : 'No articles found'}
          </h3>

          <p>
            {showSavedOnly && savedArticles.length === 0
              ? 'Use ☆ Save on any story to keep it here.'
              : 'Try another category or search term.'}
          </p>

          <button onclick={clearFilters}>
            Clear filters
          </button>

        </div>

      {/if}

    </section>

    <!-- SIDEBAR -->
    <Sidebar onTopicClick={searchTopic} />

  </div>

  {/if}
</main>

<!-- FOOTER -->
<footer class="site-footer">

  <div class="footer-inner">

    <span class="footer-logo">yahoo!</span>

    <p>
      A student UI/UX redesign project.
      Not affiliated with Yahoo.
    </p>

    <p>Built with SvelteKit</p>

  </div>

</footer>

<!-- ARTICLE PREVIEW MODAL -->
{#if selectedStory}

  <div
    class="modal-overlay"
    role="presentation"
    onclick={(event) => {
      if (event.target === event.currentTarget) {
        closeStory();
      }
    }}
  >

    <div
      class="story-modal"
      role="dialog"
      aria-modal="true"
      aria-label={selectedStory.title}
      tabindex="-1"
    >

      <button
        class="modal-close"
        onclick={closeStory}
        aria-label="Close story"
      >
        ✕
      </button>

      <img
        src={selectedStory.image}
        alt=""
      />

      <span class="modal-category">
        {selectedStory.category}
      </span>

      <h2>{selectedStory.title}</h2>

      <p class="modal-source">
        {selectedStory.source} • {selectedStory.time}
      </p>

      <p>{selectedStory.summary}</p>

      <p class="demo-note">
        This is a demo article preview.
        Full article pages can be implemented later.
      </p>

    </div>

  </div>

{/if}
