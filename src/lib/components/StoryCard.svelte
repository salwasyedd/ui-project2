
<script>
  // Svelte 5 component properties
  let {
    story,
    onRead = () => {},
    onSave = () => {},
    saved = false,
    featured = false
  } = $props();
</script>

<article class="story-card" class:featured>
  <!-- Story image -->
  <div class="story-image">
    <img
      src={story.image}
      alt=""
      loading="lazy"
    />

    <span class="category-label">
      {story.category}
    </span>
  </div>

  <!-- Story information -->
  <div class="story-content">

    <div class="story-meta">
      <span>{story.source}</span>
      <span>•</span>
      <span>{story.time}</span>
    </div>

    <h3>{story.title}</h3>

    <p>{story.summary}</p>

    <!-- Story actions -->
    <div class="story-actions">
      <button
        type="button"
        class="read-button"
        onclick={() => onRead(story)}
      >
        Read more →
      </button>

      <button
        type="button"
        class="save-button"
        class:saved
        onclick={() => onSave(story.id)}
        aria-label={saved ? 'Remove saved article' : 'Save article'}
        aria-pressed={saved}
      >
        {saved ? '★ Saved' : '☆ Save'}
      </button>
    </div>

  </div>
</article>
