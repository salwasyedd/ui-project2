
<script>

import Header from '#lib/components/Header.svelte';
import StoryCard from '#lib/components/StoryCard.svelte';
import Sidebar from '#lib/components/Sidebar.svelte';

import { stories } from '#lib/data/stories.js';


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

<!-- HEADER -->
<Header
  bind:activeCategory
  bind:searchQuery
/>

<!-- MAIN HOMEPAGE -->
<main class="page-container">

  <!-- WELCOME SECTION -->
  <section class="welcome-section">
    <div>
      <p class="eyebrow">YOUR DAILY DISCOVERIES</p>

      <h1>Stay informed. Stay inspired.</h1>

      <p class="welcome-description">
        Everything that matters to you, all in one
        clean and simple space.
      </p>
    </div>

    <div class="welcome-decoration">
      <span>✦</span>
    </div>
  </section>

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

          <p>
            Discover what's happening around you.
          </p>
        </div>

        <button
          class="view-saved-button"
          class:selected={showSavedOnly}
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
        {#if activeCategory === 'For You' && !searchQuery && !showSavedOnly}

          <div class="featured-container">
            <StoryCard
              story={filteredStories[0]}
              featured={true}
              saved={savedArticles.includes(filteredStories[0].id)}
              onRead={openStory}
              onSave={toggleSave}
            />
          </div>

        {/if}

        <!-- NEWS GRID -->
        <div class="stories-grid">

          {#each filteredStories as story (story.id)}

            {#if activeCategory !== 'For You' ||
                 searchQuery ||
                 showSavedOnly ||
                 story.id !== filteredStories[0].id}

              <StoryCard
                {story}
                saved={savedArticles.includes(story.id)}
                onRead={openStory}
                onSave={toggleSave}
              />

            {/if}

          {/each}

        </div>

      {:else}

        <!-- NO ARTICLES FOUND -->
        <div class="empty-state">

          <h3>No articles found</h3>

          <p>
            Try another category or search term.
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
    onclick={closeStory}
  >

    <div
      class="story-modal"
      role="dialog"
      aria-modal="true"
      aria-label={selectedStory.title}
      tabindex="-1"
      onclick={(event) => event.stopPropagation()}
      onkeydown={(event) => {
        if (event.key === 'Escape') {
          closeStory();
        }
      }}
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
