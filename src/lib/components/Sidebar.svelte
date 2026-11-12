
<script>
  import { trendingTopics } from '#lib/data/stories.js';

  // Svelte 5 component properties
  let {
    onTopicClick = () => {}
  } = $props();
</script>

<aside class="sidebar">

  <!-- Weather Widget -->
  <section class="sidebar-widget weather-widget">
    <div class="widget-heading">
      <h3>Weather</h3>
      <span>☀️</span>
    </div>

    <p class="weather-location">Cincinnati, OH</p>

    <div class="weather-main">
      <span class="temperature">68°F</span>
      <span class="weather-description">
        Sunny
      </span>
    </div>

    <p class="weather-note">
      Demo weather information
    </p>
  </section>

  <!-- Trending Topics -->
  <section class="sidebar-widget">
    <div class="widget-heading">
      <h3>Trending Now</h3>
      <span>↗</span>
    </div>

    <div class="trending-list">
      {#each trendingTopics as topic, index}
        <button
          type="button"
          class="trending-item"
          onclick={() => onTopicClick(topic)}
        >
          <span class="trending-number">
            {index + 1}
          </span>

          <span>{topic}</span>
        </button>
      {/each}
    </div>
  </section>

  <!-- Personalization -->
  <section class="sidebar-widget customize-widget">
    <h3>Your News, Your Way</h3>

    <p>
      A cleaner, more personalized browsing
      experience with less clutter.
    </p>

    <span class="coming-soon">
      More personalization coming soon
    </span>
  </section>

</aside>
