
<script>
  import { onMount } from 'svelte';

  // All financial data is simulated for demonstration.


  const stocks = [
    { symbol: 'AAPL', name: 'Apple Inc.', sector: 'Technology', price: 245.32, change: 1.24, cap: '3.8T', pe: '32.4', high: '260.10', low: '185.20', seed: 4 },
    { symbol: 'MSFT', name: 'Microsoft', sector: 'Technology', price: 428.15, change: 0.76, cap: '3.2T', pe: '36.8', high: '468.35', low: '344.79', seed: 8 },
    { symbol: 'NVDA', name: 'NVIDIA', sector: 'Semiconductors', price: 142.80, change: -0.82, cap: '3.5T', pe: '48.2', high: '153.13', low: '86.62', seed: 12 },
    { symbol: 'GOOGL', name: 'Alphabet', sector: 'Technology', price: 184.26, change: 0.48, cap: '2.3T', pe: '24.6', high: '207.05', low: '140.53', seed: 16 },
    { symbol: 'AMZN', name: 'Amazon', sector: 'Consumer', price: 218.75, change: 1.86, cap: '2.4T', pe: '39.1', high: '242.52', low: '161.38', seed: 20 },
    { symbol: 'TSLA', name: 'Tesla', sector: 'Automotive', price: 326.42, change: -2.15, cap: '1.0T', pe: '72.3', high: '488.54', low: '167.41', seed: 24 },
    { symbol: 'META', name: 'Meta Platforms', sector: 'Technology', price: 612.94, change: 1.32, cap: '1.5T', pe: '27.8', high: '740.91', low: '442.65', seed: 28 },
    { symbol: 'JPM', name: 'JPMorgan Chase', sector: 'Banking', price: 285.64, change: 0.65, cap: '790B', pe: '14.1', high: '301.29', low: '202.18', seed: 32 },
    { symbol: 'V', name: 'Visa', sector: 'Financial Services', price: 342.18, change: 0.91, cap: '670B', pe: '33.5', high: '375.51', low: '270.14', seed: 36 },
    { symbol: 'NFLX', name: 'Netflix', sector: 'Entertainment', price: 118.52, change: -0.45, cap: '510B', pe: '42.7', high: '133.76', low: '75.12', seed: 40 },
    { symbol: 'AMD', name: 'AMD', sector: 'Semiconductors', price: 187.35, change: 2.13, cap: '300B', pe: '54.2', high: '215.43', low: '112.80', seed: 44 },
    { symbol: 'COST', name: 'Costco', sector: 'Retail', price: 965.27, change: -0.31, cap: '425B', pe: '49.5', high: '1078.23', low: '820.51', seed: 48 }
  ];

  const indices = [
    { name: 'S&P 500', value: '5,761.67', change: -0.40 },
    { name: 'Dow Jones', value: '52,347.55', change: 0.10 },
    { name: 'Nasdaq', value: '25,136.27', change: -0.91 },
    { name: 'Russell 2000', value: '2,790.49', change: -0.31 },
    { name: 'Gold', value: '3,886.30', change: 0.03 },
    { name: 'Bitcoin', value: '65,768.74', change: -2.45 }
  ];

  const news = [
    {
      id: 1,
      category: 'MARKETS',
      title: 'The forces shaping the next era of investing',
      description: 'From artificial intelligence to economic policy, explore the major themes influencing global markets.',
      image: 'https://images.unsplash.com/photo-1611974789855-9c2a0a7236a3?w=1000&auto=format&fit=crop',
      read: '5 min read'
    },
    {
      id: 2,
      category: 'TECHNOLOGY',
      title: 'Why technology remains at the center of market attention',
      description: 'A closer look at innovation, growth, and the companies driving the digital economy.',
      image: 'https://images.unsplash.com/photo-1518770660439-4636190af475?w=700&auto=format&fit=crop',
      read: '4 min read'
    },
    {
      id: 3,
      category: 'PERSONAL FINANCE',
      title: 'Building smarter financial habits for the future',
      description: 'Discover simple approaches to budgeting, saving, and understanding investment risk.',
      image: 'https://images.unsplash.com/photo-1497366754035-f200968a6e72?w=700&auto=format&fit=crop',
      read: '6 min read'
    }
  ];

  const periods = ['1W', '1M', '3M', '1Y'];

  // =====================================================
  // STATE
  // =====================================================

  let favorites = $state(['AAPL', 'MSFT', 'NVDA']);
  let search = $state('');
  let showOnlyFavorites = $state(false);

  let selectedStock = $state(null);
  let selectedNews = $state(null);
  let selectedPeriod = $state('1M');

  let darkMode = $state(false);
  let preferencesLoaded = $state(false);

  // =====================================================
  // LOCAL STORAGE
  // =====================================================

  onMount(() => {
    try {
      const storedFavorites = localStorage.getItem(
        'yahoo-finance-favorites'
      );

      if (storedFavorites !== null) {
        const parsed = JSON.parse(storedFavorites);

        if (Array.isArray(parsed)) {
          favorites = parsed.filter(
            (symbol) =>
              typeof symbol === 'string' &&
              stocks.some((stock) => stock.symbol === symbol)
          );
        }
      }

      const savedTheme = localStorage.getItem(
        'yahoo-finance-theme'
      );

      if (savedTheme === 'dark') {
        darkMode = true;
      } else if (savedTheme === 'light') {
        darkMode = false;
      } else {
        darkMode = window.matchMedia(
          '(prefers-color-scheme: dark)'
        ).matches;
      }
    } catch (error) {
      console.warn('Could not restore preferences:', error);
    }

    preferencesLoaded = true;
  });

  $effect(() => {
    if (!preferencesLoaded) return;

    try {
      localStorage.setItem(
        'yahoo-finance-favorites',
        JSON.stringify(favorites)
      );
    } catch (error) {
      console.warn('Could not save favorites:', error);
    }
  });

  $effect(() => {
    if (!preferencesLoaded) return;

    try {
      localStorage.setItem(
        'yahoo-finance-theme',
        darkMode ? 'dark' : 'light'
      );
    } catch (error) {
      console.warn('Could not save theme:', error);
    }
  });

  // =====================================================
  // REACTIVE DATA
  // =====================================================

  let filteredStocks = $derived(
    stocks.filter((stock) => {
      const query = search.toLowerCase().trim();

      const matchesSearch =
        stock.name.toLowerCase().includes(query) ||
        stock.symbol.toLowerCase().includes(query) ||
        stock.sector.toLowerCase().includes(query);

      const matchesFavorites =
        !showOnlyFavorites ||
        favorites.includes(stock.symbol);

      return matchesSearch && matchesFavorites;
    })
  );

  let favoriteStocks = $derived(
    favorites
      .map((symbol) =>
        stocks.find((stock) => stock.symbol === symbol)
      )
      .filter(Boolean)
  );

  // =====================================================
  // ACTIONS
  // =====================================================

  function toggleTheme() {
    darkMode = !darkMode;
  }

  function toggleFavorite(symbol) {
    if (favorites.includes(symbol)) {
      favorites = favorites.filter(
        (item) => item !== symbol
      );
    } else {
      favorites = [...favorites, symbol];
    }
  }

  function openStock(stock) {
    selectedStock = stock;
    selectedPeriod = '1M';
  }

  function closePopups() {
    selectedStock = null;
    selectedNews = null;
  }

  function clearSearch() {
    search = '';
    showOnlyFavorites = false;
  }

  function formatMoney(value) {
    return '$' + Number(value).toLocaleString('en-US', {
      minimumFractionDigits: 2,
      maximumFractionDigits: 2
    });
  }

  function formatChange(value) {
    return `${value >= 0 ? '+' : ''}${value.toFixed(2)}%`;
  }

  // =====================================================
  // DEMO CHART GENERATION
  // =====================================================

  function makeSeries(stock, period = '1M') {
    if (!stock) return [];

    const counts = {
      '1W': 16,
      '1M': 30,
      '3M': 45,
      '1Y': 60
    };

    const scales = {
      '1W': 0.025,
      '1M': 0.07,
      '3M': 0.14,
      '1Y': 0.25
    };

    const count = counts[period] || 30;
    const scale = scales[period] || 0.07;

    const raw = Array.from(
      { length: count },
      (_, index) => {
        const t = index / (count - 1);

        const wave =
          Math.sin(index * 0.63 + stock.seed) * 0.29 +
          Math.sin(index * 1.47 + stock.seed * 1.3) * 0.12 +
          Math.cos(index * 0.23 + stock.seed) * 0.35;

        const trend =
          (t - 0.5) *
          (stock.change >= 0 ? 0.85 : -0.75);

        return wave + trend;
      }
    );

    const endValue = raw[raw.length - 1];

    return raw.map((value) =>
      Math.max(
        1,
        stock.price * (1 + (value - endValue) * scale)
      )
    );
  }

  function linePath(
    values,
    width = 600,
    height = 230,
    padding = 15
  ) {
    if (!values.length) return '';

    const minimum = Math.min(...values);
    const maximum = Math.max(...values);
    const range = maximum - minimum || 1;

    return values.map((value, index) => {
      const x =
        padding +
        (index / Math.max(1, values.length - 1)) *
        (width - padding * 2);

      const y =
        height -
        padding -
        ((value - minimum) / range) *
        (height - padding * 2);

      return `${index === 0 ? 'M' : 'L'} ${x.toFixed(2)} ${y.toFixed(2)}`;
    }).join(' ');
  }

  let chartValues = $derived(
    makeSeries(selectedStock, selectedPeriod)
  );

  let chartPath = $derived(
    linePath(chartValues)
  );

  let chartLow = $derived(
    chartValues.length ? Math.min(...chartValues) : 0
  );

  let chartHigh = $derived(
    chartValues.length ? Math.max(...chartValues) : 0
  );

  let periodChange = $derived(
    chartValues.length > 1
      ? (
          chartValues[chartValues.length - 1] /
          chartValues[0] - 1
        ) * 100
      : 0
  );
</script>

<svelte:head>
  <title>Yahoo Finance | A Refined Market Experience</title>
  <meta
    name="description"
    content="A premium, interactive Yahoo Finance redesign."
  />
</svelte:head>

<svelte:window
  onkeydown={(event) => {
    if (event.key === 'Escape') {
      closePopups();
    }
  }}
/>

<div class="finance-luxury" class:dark-theme={darkMode}>

  <!-- ============================================= -->
  <!-- HEADER -->
  <!-- ============================================= -->

  <header class="lux-header">
    <div class="lux-nav">

      <a href="/" class="brand" aria-label="Yahoo Home">
        <span>yahoo!</span><strong>finance</strong>
      </a>

      <nav class="nav-links" aria-label="Finance navigation">
        <a href="/">Home</a>
        <a href="#discover">Discover</a>
        <a href="#markets">Markets</a>
        <a href="#watchlist">Watchlist</a>
      </nav>

      <div class="nav-actions">

        <!-- DARK / LIGHT MODE -->
        <button
          type="button"
          class="theme-toggle"
          onclick={toggleTheme}
          aria-label={darkMode
            ? 'Switch to light mode'
            : 'Switch to dark mode'}
          aria-pressed={darkMode}
          title={darkMode
            ? 'Switch to light mode'
            : 'Switch to dark mode'}
        >
          <span class="theme-icon">
            {darkMode ? '☀' : '☾'}
          </span>

          <span class="theme-label">
            {darkMode ? 'Light Mode' : 'Dark Mode'}
          </span>
        </button>

        <a href="/" class="back-home">
          ← Main Homepage
        </a>

      </div>
    </div>
  </header>

  <!-- ============================================= -->
  <!-- MARKET TICKER -->
  <!-- ============================================= -->

  <div class="lux-market-strip">
    <div class="market-strip-inner">

      <span class="strip-label">
        <span class="live-dot"></span>
        MARKET PULSE
      </span>

      <div class="market-scroll">
        {#each indices as index}
          <div class="market-chip">
            <span>{index.name}</span>

            <strong>{index.value}</strong>

            <small
              class:gain={index.change >= 0}
              class:loss={index.change < 0}
            >
              {formatChange(index.change)}
            </small>
          </div>
        {/each}
      </div>

    </div>
  </div>

  <main class="lux-main">

    <!-- ============================================= -->
    <!-- HERO -->
    <!-- ============================================= -->

    <section class="lux-hero">

      <div class="hero-copy">

        <span class="overline">
          <span class="gold-line"></span>
          A BETTER PERSPECTIVE ON FINANCE
        </span>

        <h1>
          Your wealth.<br />
          <em>In focus.</em>
        </h1>

        <p>
          The markets move fast. Your financial experience
          shouldn't feel overwhelming. Discover everything
          that matters, thoughtfully organized in one place.
        </p>

        <div class="hero-actions">
          <a href="#markets" class="primary-cta">
            Explore the markets <span>↗</span>
          </a>

          <a href="#watchlist" class="secondary-cta">
            My watchlist →
          </a>
        </div>

        <span class="hero-footnote">
          A MORE INTENTIONAL WAY TO STAY INFORMED
        </span>

      </div>

      <div class="hero-dashboard">

        <div class="dashboard-top">
          <div>
            <span class="dash-overline">MARKET INSIGHTS</span>
            <h3>A clearer view of the market</h3>
          </div>

          <span class="dash-status">● DEMO</span>
        </div>

        <div class="hero-chart-art" aria-hidden="true">
          <svg viewBox="0 0 500 200" preserveAspectRatio="none">

            <defs>
              <linearGradient
                id="heroFill"
                x1="0"
                y1="0"
                x2="0"
                y2="1"
              >
                <stop
                  offset="0%"
                  stop-color="#C8AD7F"
                  stop-opacity=".34"
                />
                <stop
                  offset="100%"
                  stop-color="#C8AD7F"
                  stop-opacity="0"
                />
              </linearGradient>
            </defs>

            <path
              d="M0 175 H500 M0 125 H500 M0 75 H500 M0 25 H500"
              fill="none"
              stroke="#ffffff"
              stroke-opacity=".09"
              stroke-dasharray="4 5"
            />

            <path
              d="M0 170 L34 145 L66 151 L103 110 L137 125 L171 95 L203 102 L240 67 L276 82 L310 52 L344 62 L376 34 L413 49 L458 21 L500 10 L500 200 L0 200Z"
              fill="url(#heroFill)"
            />

            <path
              d="M0 170 L34 145 L66 151 L103 110 L137 125 L171 95 L203 102 L240 67 L276 82 L310 52 L344 62 L376 34 L413 49 L458 21 L500 10"
              stroke="#D7B985"
              stroke-width="3"
              fill="none"
              stroke-linecap="round"
              stroke-linejoin="round"
            />

          </svg>
        </div>

        <div class="dashboard-bottom">
          <span>Intelligent design. Less distraction.</span>
          <span>↗</span>
        </div>

      </div>

    </section>

    <!-- ============================================= -->
    <!-- SUMMARY -->
    <!-- ============================================= -->

    <section class="summary-bar" aria-label="Dashboard summary">

      <div class="summary-item">
        <span>EXPLORE</span>
        <strong>{stocks.length} Companies</strong>
        <small>Across multiple sectors</small>
      </div>

      <div class="summary-item">
        <span>MY COLLECTION</span>
        <strong>{favorites.length} Favorites</strong>
        <small>Your personal watchlist</small>
      </div>

      <div class="summary-item">
        <span>MARKET DATA</span>
        <strong>Demo Mode</strong>
        <small>Illustrative price histories</small>
      </div>

    </section>

    <!-- ============================================= -->
    <!-- STOCK EXPLORER -->
    <!-- ============================================= -->

    <section class="stock-section" id="markets">

      <div class="section-header">
        <div>
          <span class="section-kicker">
            01 / THE MARKET
          </span>

          <h2>
            Discover the market<span class="gold-period">.</span>
          </h2>

          <p>
            Explore companies, follow your favorites,
            and uncover their stories.
          </p>
        </div>

        <span class="section-count">
          {filteredStocks.length} COMPANIES
        </span>
      </div>

      <div class="explorer-panel">

        <!-- SEARCH AND FILTER -->
        <div class="explorer-toolbar">

          <div class="explorer-search">
            <span aria-hidden="true">⌕</span>

            <input
              type="search"
              placeholder="Search companies, symbols, or sectors..."
              aria-label="Search stocks"
              bind:value={search}
            />
          </div>

          <div class="explorer-controls">

            <button
              type="button"
              class:control-active={!showOnlyFavorites}
              aria-pressed={!showOnlyFavorites}
              onclick={() => showOnlyFavorites = false}
            >
              All stocks
            </button>

            <button
              type="button"
              class:control-active={showOnlyFavorites}
              aria-pressed={showOnlyFavorites}
              onclick={() => showOnlyFavorites = true}
            >
              ★ Favorites ({favorites.length})
            </button>

          </div>
        </div>

        <!-- STOCK TABLE -->
        <div class="stock-table-scroll">

          <table class="stock-table">

            <thead>
              <tr>
                <th>COMPANY</th>
                <th>PRICE</th>
                <th>DAY CHANGE</th>
                <th>30-DAY TREND</th>
                <th>FAVORITE</th>
              </tr>
            </thead>

            <tbody>

              {#each filteredStocks as stock (stock.symbol)}

                <tr
                  class:favorite-row={favorites.includes(stock.symbol)}
                >

                  <td>
                    <button
                      type="button"
                      class="company-button"
                      onclick={() => openStock(stock)}
                    >
                      <span class="stock-avatar">
                        {stock.symbol.slice(0, 1)}
                      </span>

                      <span class="company-text">
                        <strong>{stock.symbol}</strong>
                        <small>{stock.name}</small>
                      </span>
                    </button>
                  </td>

                  <td class="price-cell">
                    {formatMoney(stock.price)}
                  </td>

                  <td>
                    <span
                      class="change-pill"
                      class:up={stock.change >= 0}
                      class:down={stock.change < 0}
                    >
                      {formatChange(stock.change)}
                    </span>
                  </td>

                  <td>
                    <button
                      type="button"
                      class="sparkline-button"
                      aria-label={`View ${stock.symbol} chart`}
                      onclick={() => openStock(stock)}
                    >
                      <svg
                        viewBox="0 0 120 40"
                        aria-hidden="true"
                      >
                        <path
                          d={linePath(
                            makeSeries(stock, '1M'),
                            120,
                            40,
                            3
                          )}
                          stroke={stock.change >= 0
                            ? '#37B99D'
                            : '#E77886'}
                          stroke-width="2"
                          fill="none"
                          stroke-linecap="round"
                          stroke-linejoin="round"
                        />
                      </svg>

                      <span>View ↗</span>
                    </button>
                  </td>

                  <td>
                    <button
                      type="button"
                      class="favorite-button"
                      class:is-favorite={favorites.includes(stock.symbol)}
                      aria-label={favorites.includes(stock.symbol)
                        ? `Remove ${stock.symbol} from favorites`
                        : `Add ${stock.symbol} to favorites`}
                      aria-pressed={favorites.includes(stock.symbol)}
                      onclick={() => toggleFavorite(stock.symbol)}
                    >
                      {favorites.includes(stock.symbol) ? '★' : '☆'}
                    </button>
                  </td>

                </tr>

              {/each}

            </tbody>
          </table>

        </div>

        {#if filteredStocks.length === 0}
          <div class="empty-message">
            <span>◇</span>

            <h3>No stocks found</h3>

            <p>
              Try another company or switch back to all stocks.
            </p>

            <button type="button" onclick={clearSearch}>
              Reset search
            </button>
          </div>
        {/if}

        <div class="explorer-footer">
          <span>
            ◇ Click a company or chart to explore its performance.
          </span>

          <span>Illustrative market data only</span>
        </div>

      </div>
    </section>

    <!-- ============================================= -->
    <!-- FAVORITES / WATCHLIST -->
    <!-- ============================================= -->

    <section class="watchlist-section" id="watchlist">

      <div class="section-header">
        <div>
          <span class="section-kicker">
            02 / CURATED BY YOU
          </span>

          <h2>
            Your watchlist<span class="gold-period">.</span>
          </h2>

          <p>
            A personal collection of the companies
            you want to follow.
          </p>
        </div>

        <span class="section-count">
          {favorites.length} SAVED
        </span>
      </div>

      {#if favoriteStocks.length > 0}

        <div class="favorites-grid">

          {#each favoriteStocks as stock (stock.symbol)}

            <article class="favorite-card">

              <div class="favorite-card-top">

                <span class="stock-avatar large">
                  {stock.symbol.slice(0, 1)}
                </span>

                <button
                  type="button"
                  class="remove-favorite"
                  onclick={() => toggleFavorite(stock.symbol)}
                  aria-label={`Remove ${stock.symbol} from favorites`}
                >
                  ★
                </button>

              </div>

              <span class="favorite-symbol">
                {stock.symbol}
              </span>

              <h3>{stock.name}</h3>

              <span class="favorite-sector">
                {stock.sector}
              </span>

              <div class="favorite-card-chart">

                <svg
                  viewBox="0 0 260 75"
                  preserveAspectRatio="none"
                  aria-label={`Demo price trend for ${stock.symbol}`}
                  role="img"
                >
                  <path
                    d={linePath(
                      makeSeries(stock, '1M'),
                      260,
                      75,
                      5
                    )}
                    fill="none"
                    stroke={stock.change >= 0
                      ? '#39BEA3'
                      : '#E77886'}
                    stroke-width="2.5"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                  />
                </svg>

              </div>

              <div class="favorite-card-bottom">

                <div>
                  <strong>
                    {formatMoney(stock.price)}
                  </strong>

                  <span
                    class:gain={stock.change >= 0}
                    class:loss={stock.change < 0}
                  >
                    {formatChange(stock.change)}
                  </span>
                </div>

                <button
                  type="button"
                  onclick={() => openStock(stock)}
                >
                  Explore ↗
                </button>

              </div>

            </article>

          {/each}

        </div>

      {:else}

        <div class="watchlist-empty">

          <span>☆</span>

          <h3>Your collection starts here.</h3>

          <p>
            Choose the star beside any company
            to add it to your personal watchlist.
          </p>

          <a href="#markets">
            Browse stocks →
          </a>

        </div>

      {/if}

    </section>

    <!-- ============================================= -->
    <!-- FINANCIAL NEWS -->
    <!-- ============================================= -->

    <section class="news-section" id="discover">

      <div class="section-header">
        <div>

          <span class="section-kicker">
            03 / BEYOND THE NUMBERS
          </span>

          <h2>
            Market perspectives<span class="gold-period">.</span>
          </h2>

          <p>
            Financial stories, presented with clarity and intention.
          </p>

        </div>
      </div>

      <div class="news-grid">

        {#each news as article, index (article.id)}

          <article
            class="editorial-card"
            class:editorial-featured={index === 0}
          >

            <div class="editorial-image">

              <img
                src={article.image}
                alt=""
                loading="lazy"
              />

              <span class="editorial-tag">
                {article.category}
              </span>

            </div>

            <div class="editorial-content">

              <span class="read-time">
                {article.read} · DEMO ARTICLE
              </span>

              <h3>{article.title}</h3>

              <p>{article.description}</p>

              <button
                type="button"
                onclick={() => selectedNews = article}
              >
                Read preview <span>↗</span>
              </button>

            </div>

          </article>

        {/each}

      </div>

    </section>

  </main>

  <!-- ============================================= -->
  <!-- FOOTER -->
  <!-- ============================================= -->

  <footer class="lux-footer">

    <div class="footer-inner">

      <div>
        <span class="footer-brand">
          yahoo!<strong>finance</strong>
        </span>

        <p>
          Financial information, thoughtfully redesigned.
        </p>
      </div>

      <div class="footer-meta">
        <span>STUDENT UI/UX PROJECT</span>
        <span>NOT AFFILIATED WITH YAHOO</span>
        <span>SIMULATED MARKET DATA</span>
      </div>

    </div>

  </footer>

  <!-- ============================================= -->
  <!-- STOCK DETAILS POPUP -->
  <!-- ============================================= -->

  {#if selectedStock}

    <div class="modal-backdrop">

      <button
        type="button"
        class="backdrop-dismiss"
        aria-label="Close stock popup"
        onclick={closePopups}
      ></button>

      <section
        class="stock-modal"
        role="dialog"
        aria-modal="true"
        aria-label={`${selectedStock.name} stock details`}
        tabindex="-1"
      >

        <div class="modal-header">

          <div class="modal-company">

            <span class="stock-avatar large">
              {selectedStock.symbol.slice(0, 1)}
            </span>

            <div>
              <h2>{selectedStock.name}</h2>

              <span>
                {selectedStock.symbol} · {selectedStock.sector}
              </span>
            </div>

          </div>

          <button
            type="button"
            class="close-modal"
            onclick={closePopups}
            aria-label="Close stock details"
          >
            ✕
          </button>

        </div>

        <div class="modal-price-row">

          <div>
            <span class="modal-eyebrow">
              DEMO SHARE PRICE · USD
            </span>

            <h3>{formatMoney(selectedStock.price)}</h3>

            <span
              class:gain={selectedStock.change >= 0}
              class:loss={selectedStock.change < 0}
            >
              {formatChange(selectedStock.change)}
              simulated day change
            </span>
          </div>

          <button
            type="button"
            class="modal-fav-btn"
            class:already-saved={favorites.includes(selectedStock.symbol)}
            onclick={() => toggleFavorite(selectedStock.symbol)}
          >
            {favorites.includes(selectedStock.symbol)
              ? '★ In watchlist'
              : '☆ Add to watchlist'}
          </button>

        </div>

        <!-- INTERACTIVE CHART -->
        <div class="chart-panel">

          <div class="chart-toolbar">

            <div>
              <span class="modal-eyebrow">
                SIMULATED PERFORMANCE
              </span>

              <strong
                class:gain={periodChange >= 0}
                class:loss={periodChange < 0}
              >
                {formatChange(periodChange)} · {selectedPeriod}
              </strong>
            </div>

            <div
              class="period-tabs"
              aria-label="Select chart period"
            >

              {#each periods as period}

                <button
                  type="button"
                  class:period-selected={selectedPeriod === period}
                  aria-pressed={selectedPeriod === period}
                  onclick={() => selectedPeriod = period}
                >
                  {period}
                </button>

              {/each}

            </div>

          </div>

          <div class="chart-area">

            <svg
              viewBox="0 0 600 230"
              preserveAspectRatio="none"
              role="img"
              aria-label={`Simulated ${selectedPeriod} price history for ${selectedStock.symbol}`}
            >

              <path
                d="M15 50 H585 M15 105 H585 M15 160 H585"
                stroke="#ffffff"
                stroke-opacity=".09"
                stroke-dasharray="4 6"
              />

              <path
                d={chartPath}
                stroke={periodChange >= 0
                  ? '#43D0B3'
                  : '#F18B99'}
                stroke-width="3.5"
                stroke-linejoin="round"
                stroke-linecap="round"
                fill="none"
              />

            </svg>

          </div>

          <div class="chart-legend">

            <span>
              LOW {formatMoney(chartLow)}
            </span>

            <span>DEMO DATA · NOT LIVE</span>

            <span>
              HIGH {formatMoney(chartHigh)}
            </span>

          </div>

        </div>

        <!-- COMPANY STATISTICS -->
        <div class="stock-metrics">

          <div>
            <span>Market Cap</span>
            <strong>${selectedStock.cap}</strong>
          </div>

          <div>
            <span>P/E Ratio</span>
            <strong>{selectedStock.pe}</strong>
          </div>

          <div>
            <span>52-Week High</span>
            <strong>${selectedStock.high}</strong>
          </div>

          <div>
            <span>52-Week Low</span>
            <strong>${selectedStock.low}</strong>
          </div>

        </div>

        <p class="modal-disclaimer">
          All figures and price histories are illustrative.
          This prototype does not provide live trading
          information or investment advice.
        </p>

      </section>

    </div>

  {/if}

  <!-- ============================================= -->
  <!-- NEWS PREVIEW POPUP -->
  <!-- ============================================= -->

  {#if selectedNews}

    <div class="modal-backdrop">

      <button
        type="button"
        class="backdrop-dismiss"
        aria-label="Close article preview"
        onclick={closePopups}
      ></button>

      <section
        class="news-modal"
        role="dialog"
        aria-modal="true"
        aria-label={selectedNews.title}
        tabindex="-1"
      >

        <button
          type="button"
          class="close-modal news-close"
          onclick={closePopups}
          aria-label="Close article"
        >
          ✕
        </button>

        <img src={selectedNews.image} alt="" />

        <span class="section-kicker">
          {selectedNews.category}
        </span>

        <h2>{selectedNews.title}</h2>

        <p>{selectedNews.description}</p>

        <div class="article-notice">
          This is a sample editorial preview.
          A future version can integrate real financial
          reporting and article pages.
        </div>

      </section>

    </div>

  {/if}

</div>

<style>
  /* ==========================================
     BASE THEME
  ========================================== */

  :global(html) {
    scroll-behavior: smooth;
  }

  .finance-luxury {
    --navy: #111827;
    --navy-soft: #1c2435;
    --purple: #6032b1;
    --gold: #c6a46b;
    --mint: #35b89a;
    --ivory: #f7f5f0;
    --ink: #222535;
    --muted: #777b89;
    --line: #e9e7e3;

    --surface: #ffffff;
    --surface-muted: #f5f4f7;
    --surface-hover: #f8f6fb;
    --surface-favorite: #faf8fd;

    min-height: 100vh;
    background: var(--ivory);
    color: var(--ink);
    font-family: 'Inter', Arial, sans-serif;

    transition:
      background-color .3s ease,
      color .3s ease;
  }

  .finance-luxury * {
    box-sizing: border-box;
  }

  .finance-luxury button,
  .finance-luxury input {
    font: inherit;
  }

  .finance-luxury button {
    cursor: pointer;
  }

  .finance-luxury a {
    text-decoration: none;
  }

  /* ==========================================
     DARK MODE VARIABLES
  ========================================== */

  .finance-luxury.dark-theme {
    --ivory: #0e1421;
    --ink: #f3f1f8;
    --muted: #a8adbf;
    --line: #323a4d;
    --purple: #c3a1fa;

    --surface: #1a2233;
    --surface-muted: #252e42;
    --surface-hover: #273047;
    --surface-favorite: #29233d;

    background: var(--ivory);
    color: var(--ink);
  }

  /* ==========================================
     HEADER
  ========================================== */

  .lux-header {
    background: var(--surface);
    border-bottom: 1px solid var(--line);
    transition: background .3s ease;
  }

  .lux-nav {
    max-width: 1360px;
    margin: auto;
    padding: 22px 28px;

    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 25px;
  }

  .brand {
    display: flex;
    align-items: baseline;
    white-space: nowrap;
    letter-spacing: -1px;
  }

  .brand span {
    color: var(--purple);
    font-size: 26px;
    font-weight: 800;
  }

  .brand strong {
    font-size: 23px;
    color: var(--ink);
  }

  .nav-links {
    display: flex;
    gap: 35px;
  }

  .nav-links a {
    font-size: 13px;
    font-weight: 600;
    color: var(--muted);
    transition: color .2s;
  }

  .nav-links a:hover {
    color: var(--purple);
  }

  .nav-actions {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .theme-toggle {
    display: flex;
    align-items: center;
    gap: 9px;

    background: var(--surface-muted);
    border: 1px solid var(--line);
    color: var(--purple);

    padding: 11px 15px;
    border-radius: 10px;
    font-size: 12px;
    font-weight: 700;

    white-space: nowrap;
    transition: all .25s ease;
  }

  .theme-toggle:hover {
    transform: translateY(-2px);
    border-color: var(--purple);
  }

  .theme-icon {
    font-size: 18px;
    line-height: 1;
  }

  .theme-label {
    font-size: 11px;
  }

  .back-home {
    color: var(--purple);
    font-weight: 700;
    font-size: 12px;

    border: 1px solid var(--line);
    padding: 12px 16px;
    border-radius: 9px;

    white-space: nowrap;
  }

  /* ==========================================
     MARKET TICKER
  ========================================== */

  .lux-market-strip {
    background: #111827;
    color: white;
  }

  .market-strip-inner {
    max-width: 1360px;
    margin: auto;
    padding: 18px 28px;

    display: flex;
    align-items: center;
    gap: 35px;
  }

  .strip-label {
    font-size: 10px;
    font-weight: 800;
    letter-spacing: 1.4px;
    white-space: nowrap;
    color: #d5c4a6;

    display: flex;
    align-items: center;
    gap: 9px;
  }

  .live-dot {
    width: 7px;
    height: 7px;
    background: #c6a46b;
    border-radius: 50%;
  }

  .market-scroll {
    display: flex;
    gap: 32px;
    overflow-x: auto;
    width: 100%;
    scrollbar-width: thin;
  }

  .market-chip {
    display: flex;
    flex-direction: column;
    gap: 5px;

    min-width: 115px;
    padding: 2px 0;
    white-space: nowrap;
  }

  .market-chip > span {
    color: #afb4c3;
    font-size: 11px;
  }

  .market-chip strong {
    font-size: 14px;
  }

  .market-chip small {
    font-weight: 700;
  }

  .gain {
    color: #36c4a5 !important;
  }

  .loss {
    color: #e87083 !important;
  }

  /* ==========================================
     MAIN CONTENT
  ========================================== */

  .lux-main {
    max-width: 1360px;
    margin: auto;
    padding: 40px 28px 90px;
  }

  /* ==========================================
     HERO
  ========================================== */

  .lux-hero {
    background: linear-gradient(
      120deg,
      #101726,
      #1e2840 67%,
      #342657
    );

    color: white;
    border-radius: 22px;
    padding: 65px;

    display: grid;
    grid-template-columns: 1.1fr .9fr;
    align-items: center;
    gap: 65px;

    position: relative;
    overflow: hidden;
  }

  .lux-hero::after {
    content: '';
    position: absolute;

    top: -170px;
    right: -120px;

    height: 420px;
    width: 420px;

    border-radius: 50%;
    background: radial-gradient(
      circle,
      #7950c040,
      transparent 70%
    );

    pointer-events: none;
  }

  .overline {
    color: #d0b889;
    font-size: 10px;
    font-weight: 800;
    letter-spacing: 2px;

    display: flex;
    align-items: center;
    gap: 12px;
  }

  .gold-line {
    width: 25px;
    height: 1px;
    background: #c6a46b;
  }

  .hero-copy h1 {
    font-size: clamp(40px, 4.4vw, 65px);
    line-height: 1.12;
    letter-spacing: -2px;
    margin: 23px 0;
    font-weight: 700;
  }

  .hero-copy h1 em {
    font-style: normal;
    color: #d2b788;
  }

  .hero-copy p {
    color: #c0c5d1;
    max-width: 485px;

    font-size: 14px;
    line-height: 1.95;
  }

  .hero-actions {
    display: flex;
    gap: 20px;
    align-items: center;
    flex-wrap: wrap;
    margin-top: 35px;
  }

  .primary-cta {
    display: inline-flex;
    gap: 23px;
    align-items: center;

    background: #c8ab79;
    color: #151b2a;

    padding: 15px 22px;
    border-radius: 9px;

    font-size: 12px;
    font-weight: 800;
  }

  .secondary-cta {
    color: #eee7dc;
    font-size: 12px;
    font-weight: 700;
  }

  .hero-footnote {
    display: block;
    color: #8e96a9;
    font-size: 9px;
    font-weight: 700;
    letter-spacing: 1.7px;
    margin-top: 45px;
  }

  .hero-dashboard {
    background: #ffffff0a;
    border: 1px solid #ffffff24;

    padding: 28px;
    border-radius: 18px;

    position: relative;
    z-index: 1;
  }

  .dashboard-top,
  .dashboard-bottom {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    gap: 12px;
  }

  .dash-overline {
    color: #d1b887;
    font-size: 10px;
    letter-spacing: 1.5px;
    font-weight: 800;
  }

  .dashboard-top h3 {
    font-size: 17px;
    margin: 12px 0 0;
  }

  .dash-status {
    color: #d5bd8e;
    background: #c6a46b1e;

    border-radius: 20px;
    padding: 8px 10px;

    font-size: 9px;
    font-weight: 800;
  }

  .hero-chart-art svg {
    display: block;
    width: 100%;
    height: 200px;
    margin-top: 23px;
  }

  .dashboard-bottom {
    border-top: 1px solid #ffffff1a;
    padding-top: 20px;

    color: #adb5c6;
    font-size: 11px;
  }

  .dashboard-bottom span:last-child {
    font-size: 17px;
    color: #c6a46b;
  }

  /* ==========================================
     SUMMARY BAR
  ========================================== */

  .summary-bar {
    display: grid;
    grid-template-columns: repeat(3, 1fr);

    background: var(--surface);
    border: 1px solid var(--line);

    border-radius: 15px;
    margin-top: 25px;
    padding: 26px;
  }

  .summary-item {
    padding: 4px 30px;

    display: flex;
    flex-direction: column;
    gap: 9px;
  }

  .summary-item:not(:last-child) {
    border-right: 1px solid var(--line);
  }

  .summary-item > span {
    color: var(--purple);
    font-weight: 800;
    font-size: 10px;
    letter-spacing: 1.2px;
  }

  .summary-item strong {
    font-size: 22px;
    color: var(--ink);
  }

  .summary-item small {
    color: var(--muted);
    font-size: 11px;
  }

  /* ==========================================
     SECTION HEADINGS
  ========================================== */

  .stock-section,
  .watchlist-section,
  .news-section {
    margin-top: 90px;
    scroll-margin-top: 25px;
  }

  .section-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
    gap: 20px;
    margin-bottom: 30px;
  }

  .section-kicker {
    color: var(--purple);
    font-size: 10px;
    font-weight: 800;
    letter-spacing: 1.8px;
  }

  .section-header h2 {
    font-size: clamp(26px, 3vw, 39px);
    letter-spacing: -1px;
    margin: 12px 0;
    color: var(--ink);
  }

  .gold-period {
    color: #c6a46b;
  }

  .section-header p {
    color: var(--muted);
    font-size: 13px;
    margin: 0;
    line-height: 1.7;
  }

  .section-count {
    color: var(--muted);
    font-size: 10px;
    font-weight: 800;
    letter-spacing: 1.5px;
    white-space: nowrap;
  }

  /* ==========================================
     STOCK EXPLORER
  ========================================== */

  .explorer-panel {
    background: var(--surface);
    border: 1px solid var(--line);
    border-radius: 18px;
    overflow: hidden;
  }

  .explorer-toolbar {
    padding: 23px 25px;
    border-bottom: 1px solid var(--line);

    display: flex;
    align-items: center;
    gap: 20px;
    justify-content: space-between;
  }

  .explorer-search {
    display: flex;
    gap: 12px;
    align-items: center;

    background: var(--surface-muted);
    border: 1px solid var(--line);
    border-radius: 10px;

    padding: 0 15px;
    flex: 1;
    max-width: 430px;
  }

  .explorer-search span {
    color: var(--purple);
    font-size: 25px;
  }

  .explorer-search input {
    border: none;
    outline: none;
    background: transparent;

    width: 100%;
    padding: 14px 0;

    font-size: 12px;
    color: var(--ink);
  }

  .explorer-search input::placeholder {
    color: var(--muted);
  }

  .explorer-controls {
    display: flex;
    gap: 8px;
  }

  .explorer-controls button {
    border: 1px solid var(--line);
    background: var(--surface);

    padding: 11px 15px;
    border-radius: 9px;

    font-size: 11px;
    font-weight: 700;
    color: var(--muted);
    white-space: nowrap;
  }

  .explorer-controls .control-active {
    background: #6032b1;
    color: white;
    border-color: #6032b1;
  }

  /* ==========================================
     STOCK TABLE
  ========================================== */

  .stock-table-scroll {
    overflow-x: auto;
  }

  .stock-table {
    border-collapse: collapse;
    width: 100%;
    min-width: 680px;
    text-align: left;
  }

  .stock-table th {
    font-size: 10px;
    letter-spacing: 1.4px;
    color: var(--muted);
    font-weight: 800;

    padding: 20px 24px;
    border-bottom: 1px solid var(--line);
    white-space: nowrap;
  }

  .stock-table td {
    border-bottom: 1px solid var(--line);
    padding: 15px 24px;
    vertical-align: middle;
  }

  .stock-table tbody tr {
    transition: background .2s;
  }

  .stock-table tbody tr:hover {
    background: var(--surface-hover);
  }

  .stock-table tbody tr.favorite-row {
    background: var(--surface-favorite);
  }

  .stock-table tbody tr.favorite-row:hover {
    background: var(--surface-hover);
  }

  .company-button {
    display: flex;
    align-items: center;
    gap: 13px;

    background: none;
    border: none;
    padding: 0;
    text-align: left;
  }

  .stock-avatar {
    display: grid;
    place-items: center;

    height: 39px;
    width: 39px;
    min-width: 39px;

    border-radius: 11px;
    background: #ede5f8;
    color: #6032b1;

    font-size: 15px;
    font-weight: 800;
  }

  .dark-theme .stock-avatar {
    background: #40305c;
    color: #dfc8ff;
  }

  .stock-avatar.large {
    width: 47px;
    height: 47px;
    font-size: 18px;
  }

  .company-text {
    display: flex;
    flex-direction: column;
    gap: 5px;
  }

  .company-text strong {
    color: var(--ink);
    font-size: 13px;
  }

  .company-text small {
    color: var(--muted);
    font-size: 11px;
    white-space: nowrap;
  }

  .price-cell {
    font-weight: 800;
    color: var(--ink);
    font-size: 13px;
  }

  .change-pill {
    display: inline-block;
    border-radius: 7px;
    padding: 8px 10px;

    font-size: 11px;
    font-weight: 800;
  }

  .change-pill.up {
    background: #e4f8f1;
    color: #158c74;
  }

  .change-pill.down {
    background: #fdecef;
    color: #cc526a;
  }

  .dark-theme .change-pill.up {
    background: #193c38;
    color: #56e0c0;
  }

  .dark-theme .change-pill.down {
    background: #492d3a;
    color: #ff9ead;
  }

  .sparkline-button {
    display: flex;
    align-items: center;
    gap: 10px;

    background: none;
    border: none;

    color: var(--purple);
    font-size: 11px;
    font-weight: 700;
  }

  .sparkline-button svg {
    width: 100px;
    height: 35px;
  }

  .favorite-button {
    background: var(--surface-muted);
    border: 1px solid transparent;
    border-radius: 10px;

    height: 37px;
    width: 37px;

    font-size: 24px;
    color: #a4a1ac;
  }

  .favorite-button.is-favorite {
    color: #b08c52;
    background: #fbf4e7;
    border-color: #f1e4cb;
  }

  .dark-theme .favorite-button.is-favorite {
    background: #4b3d2a;
    border-color: #725a35;
    color: #e6c48d;
  }

  .explorer-footer {
    padding: 18px 25px;

    display: flex;
    justify-content: space-between;
    gap: 12px;
    flex-wrap: wrap;

    color: var(--muted);
    font-size: 10px;
  }

  .empty-message {
    text-align: center;
    padding: 55px 20px;
  }

  .empty-message > span {
    font-size: 40px;
    color: #c6a46b;
  }

  .empty-message h3 {
    color: var(--ink);
  }

  .empty-message p {
    color: var(--muted);
    font-size: 13px;
  }

  .empty-message button {
    background: #6032b1;
    color: white;

    padding: 11px 20px;
    border: none;
    border-radius: 8px;
    margin-top: 12px;
  }

  /* ==========================================
     FAVORITES / WATCHLIST
  ========================================== */

  .favorites-grid {
    display: grid;
    grid-template-columns: repeat(3, minmax(0, 1fr));
    gap: 20px;
  }

  .favorite-card {
    background: #111827;
    color: white;

    border-radius: 17px;
    padding: 27px;
    min-width: 0;

    transition:
      transform .2s,
      box-shadow .2s;
  }

  .favorite-card:hover {
    transform: translateY(-3px);
    box-shadow: 0 12px 30px #11182722;
  }

  .dark-theme .favorite-card {
    background: #202b40;
    border: 1px solid #394259;
  }

  .favorite-card-top {
    display: flex;
    justify-content: space-between;
  }

  .remove-favorite {
    background: #c6a46b22;
    border: 1px solid #c6a46b44;
    border-radius: 9px;

    height: 34px;
    width: 34px;

    font-size: 21px;
    color: #d8b987;
  }

  .favorite-symbol {
    display: block;
    margin-top: 24px;

    font-size: 22px;
    font-weight: 800;
  }

  .favorite-card h3 {
    margin: 6px 0;
    font-size: 13px;
    font-weight: 600;
    color: #e3e4ea;
  }

  .favorite-sector {
    color: #929cae;
    font-size: 11px;
  }

  .favorite-card-chart {
    margin: 25px 0 15px;
  }

  .favorite-card-chart svg {
    width: 100%;
    height: 80px;
  }

  .favorite-card-bottom {
    border-top: 1px solid #ffffff1d;
    padding-top: 19px;

    display: flex;
    justify-content: space-between;
    align-items: flex-end;
    gap: 15px;
  }

  .favorite-card-bottom > div {
    display: flex;
    flex-direction: column;
    gap: 7px;
  }

  .favorite-card-bottom strong {
    font-size: 19px;
  }

  .favorite-card-bottom span {
    font-size: 12px;
  }

  .favorite-card-bottom button {
    color: #d9bf90;
    background: transparent;
    border: none;

    font-size: 11px;
    font-weight: 700;
    white-space: nowrap;
  }

  .watchlist-empty {
    text-align: center;
    background: var(--surface);
    border: 1px solid var(--line);

    border-radius: 16px;
    padding: 55px 20px;
  }

  .watchlist-empty > span {
    color: #c6a46b;
    font-size: 40px;
  }

  .watchlist-empty h3 {
    color: var(--ink);
  }

  .watchlist-empty p {
    color: var(--muted);
    font-size: 13px;
  }

  .watchlist-empty a {
    display: inline-block;
    margin-top: 15px;

    color: var(--purple);
    font-weight: 700;
    font-size: 12px;
  }

  /* ==========================================
     FINANCIAL NEWS
  ========================================== */

  .news-grid {
    display: grid;
    grid-template-columns: 1.3fr 1fr 1fr;
    gap: 20px;
  }

  .editorial-card {
    background: var(--surface);
    border: 1px solid var(--line);
    border-radius: 16px;

    overflow: hidden;
    min-width: 0;
  }

  .editorial-image {
    height: 185px;
    position: relative;
    background: var(--surface-muted);
  }

  .editorial-image img {
    height: 100%;
    width: 100%;
    object-fit: cover;
    filter: saturate(.78);
  }

  .editorial-featured .editorial-image {
    height: 230px;
  }

  .editorial-tag {
    position: absolute;
    bottom: 16px;
    left: 16px;

    background: #ffffff;
    border-radius: 6px;
    padding: 8px 11px;

    color: #6032b1;
    font-size: 9px;
    font-weight: 800;
    letter-spacing: .5px;
  }

  .dark-theme .editorial-tag {
    background: #211c31;
    color: #d5b8ff;
  }

  .editorial-content {
    padding: 23px;
  }

  .read-time {
    color: var(--muted);
    font-size: 10px;
    font-weight: 700;
    letter-spacing: .5px;
  }

  .editorial-content h3 {
    font-size: 18px;
    line-height: 1.45;

    color: var(--ink);
    margin: 12px 0;
  }

  .editorial-featured .editorial-content h3 {
    font-size: 23px;
  }

  .editorial-content p {
    color: var(--muted);
    line-height: 1.7;
    font-size: 12px;
  }

  .editorial-content button {
    background: transparent;
    border: none;
    padding: 0;

    color: var(--purple);
    font-size: 12px;
    font-weight: 800;

    margin-top: 16px;
    display: flex;
    justify-content: space-between;
    width: 100%;
  }

  /* ==========================================
     FOOTER
  ========================================== */

  .lux-footer {
    background: #111827;
    color: white;
  }

  .footer-inner {
    max-width: 1360px;
    margin: auto;
    padding: 40px 28px;

    display: flex;
    justify-content: space-between;
    gap: 25px;
    align-items: center;
  }

  .footer-brand {
    color: #c9b3ee;
    font-size: 23px;
    font-weight: 800;
  }

  .footer-brand strong {
    color: white;
  }

  .footer-inner p {
    color: #929caf;
    font-size: 11px;
  }

  .footer-meta {
    display: flex;
    flex-direction: column;
    gap: 8px;

    color: #8b92a4;
    font-size: 9px;
    font-weight: 700;
    letter-spacing: 1px;
    text-align: right;
  }

  /* ==========================================
     MODAL BACKDROP
  ========================================== */

  .modal-backdrop {
    position: fixed;
    inset: 0;

    background: #090d19cc;
    z-index: 100;

    display: grid;
    place-items: center;

    padding: 20px;
    overflow-y: auto;
  }

  .backdrop-dismiss {
    position: fixed;
    inset: 0;

    width: 100%;
    height: 100%;

    background: transparent;
    border: none;
    cursor: default;
  }

  /* ==========================================
     STOCK MODAL
  ========================================== */

  .stock-modal {
    position: relative;
    z-index: 1;

    width: 100%;
    max-width: 800px;
    max-height: 92vh;
    overflow-y: auto;

    background: var(--surface);
    color: var(--ink);

    border-radius: 20px;
    padding: 30px;

    box-shadow: 0 25px 90px #0005;
  }

  .modal-header,
  .modal-company,
  .modal-price-row,
  .chart-toolbar {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 18px;
  }

  .modal-company {
    justify-content: flex-start;
  }

  .modal-company h2 {
    font-size: 20px;
    margin: 0 0 5px;
  }

  .modal-company > div > span {
    color: var(--muted);
    font-size: 12px;
  }

  .close-modal {
    border: none;
    border-radius: 50%;

    height: 36px;
    width: 36px;

    background: var(--surface-muted);
    color: var(--ink);

    flex-shrink: 0;
  }

  .modal-price-row {
    margin: 35px 0 27px;
  }

  .modal-eyebrow {
    color: var(--muted);
    font-size: 10px;
    font-weight: 800;
    letter-spacing: 1.2px;
  }

  .modal-price-row h3 {
    font-size: 35px;
    margin: 8px 0;
    letter-spacing: -1px;
  }

  .modal-price-row > div > span:last-child {
    font-size: 12px;
  }

  .modal-fav-btn {
    border: 1px solid #e9dcc1;
    background: #faf5e9;
    color: #a07b40;

    padding: 12px 15px;
    border-radius: 9px;

    font-size: 12px;
    font-weight: 800;
    white-space: nowrap;
  }

  .modal-fav-btn.already-saved {
    background: #f0e9d9;
  }

  .dark-theme .modal-fav-btn {
    background: #443821;
    color: #e8c98e;
    border-color: #74603d;
  }

  /* ==========================================
     CHART PANEL
  ========================================== */

  .chart-panel {
    background: #111827;
    border-radius: 15px;
    padding: 25px;
    color: white;
  }

  .chart-toolbar {
    align-items: flex-start;
  }

  .chart-toolbar > div:first-child {
    display: flex;
    flex-direction: column;
    gap: 9px;
  }

  .chart-toolbar strong {
    font-size: 17px;
  }

  .period-tabs {
    display: flex;
    gap: 5px;
  }

  .period-tabs button {
    border: none;
    color: #b0b6c3;
    background: #ffffff12;

    border-radius: 6px;
    padding: 9px 11px;

    font-size: 11px;
    font-weight: 800;
  }

  .period-tabs button.period-selected {
    color: #1a2130;
    background: #d6bb8d;
  }

  .chart-area svg {
    width: 100%;
    height: 230px;
    margin: 25px 0 10px;
    display: block;
  }

  .chart-legend {
    display: flex;
    justify-content: space-between;
    gap: 10px;

    color: #8e9aaf;
    font-size: 10px;
    font-weight: 700;
    letter-spacing: .5px;
  }

  /* ==========================================
     STOCK METRICS
  ========================================== */

  .stock-metrics {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 13px;
    margin-top: 23px;
  }

  .stock-metrics > div {
    border: 1px solid var(--line);
    border-radius: 9px;
    padding: 15px;

    display: flex;
    flex-direction: column;
    gap: 9px;

    background: var(--surface-muted);
  }

  .stock-metrics span {
    color: var(--muted);
    font-size: 11px;
  }

  .stock-metrics strong {
    font-size: 14px;
    color: var(--ink);
  }

  .modal-disclaimer {
    color: var(--muted);
    font-size: 10px;
    margin: 23px 0 0;
    line-height: 1.7;
  }

  /* ==========================================
     ARTICLE MODAL
  ========================================== */

  .news-modal {
    position: relative;
    z-index: 1;

    width: 100%;
    max-width: 570px;
    max-height: 90vh;
    overflow: auto;

    padding: 26px;
    border-radius: 17px;

    background: var(--surface);
    color: var(--ink);
  }

  .news-close {
    position: absolute;
    top: 38px;
    right: 38px;
  }

  .news-modal img {
    width: 100%;
    height: 230px;
    object-fit: cover;

    border-radius: 10px;
    margin-bottom: 25px;
  }

  .news-modal h2 {
    color: var(--ink);
    line-height: 1.3;
    font-size: 26px;
  }

  .news-modal p {
    color: var(--muted);
    line-height: 1.8;
  }

  .article-notice {
    background: #f3eff9;
    border-radius: 9px;
    padding: 17px;

    color: #6a568b;
    line-height: 1.6;
    font-size: 12px;
  }

  .dark-theme .article-notice {
    background: #312944;
    color: #d8c3f5;
  }

  /* ==========================================
     LIGHT / DARK TRANSITIONS
  ========================================== */

  .lux-header,
  .summary-bar,
  .explorer-panel,
  .explorer-toolbar,
  .stock-table td,
  .stock-table th,
  .editorial-card,
  .stock-modal,
  .news-modal,
  .watchlist-empty {
    transition:
      background-color .3s ease,
      color .3s ease,
      border-color .3s ease;
  }

  /* ==========================================
     RESPONSIVE — TABLET
  ========================================== */

  @media (max-width: 1050px) {
    .lux-hero {
      padding: 45px;
      gap: 25px;
    }

    .lux-nav {
      flex-wrap: wrap;
    }

    .nav-links {
      gap: 22px;
    }

    .favorites-grid {
      grid-template-columns: repeat(2, minmax(0, 1fr));
    }

    .news-grid {
      grid-template-columns: repeat(2, minmax(0, 1fr));
    }

    .editorial-featured {
      grid-column: span 2;
    }
  }

  /* ==========================================
     RESPONSIVE — MOBILE
  ========================================== */

  @media (max-width: 700px) {
    .lux-nav {
      flex-wrap: wrap;
      padding: 17px;
    }

    .brand span {
      font-size: 23px;
    }

    .brand strong {
      font-size: 21px;
    }

    .nav-actions {
      margin-left: auto;
    }

    .theme-toggle {
      padding: 10px 12px;
    }

    .theme-label {
      display: none;
    }

    .back-home {
      padding: 10px;
      font-size: 10px;
    }

    .nav-links {
      order: 3;
      width: 100%;
      gap: 22px;
      overflow-x: auto;
    }

    .market-strip-inner {
      flex-direction: column;
      align-items: stretch;
      gap: 14px;
      padding: 16px;
    }

    .lux-main {
      padding: 18px 15px 50px;
    }

    .lux-hero {
      grid-template-columns: 1fr;
      padding: 32px 25px;
    }

    .hero-copy h1 {
      font-size: 43px;
    }

    .hero-dashboard {
      padding: 20px;
    }

    .hero-chart-art svg {
      height: 150px;
    }

    .summary-bar {
      grid-template-columns: 1fr;
      padding: 14px 22px;
    }

    .summary-item {
      padding: 16px 10px;
    }

    .summary-item:not(:last-child) {
      border-right: none;
      border-bottom: 1px solid var(--line);
    }

    .stock-section,
    .watchlist-section,
    .news-section {
      margin-top: 60px;
    }

    .section-header {
      align-items: flex-start;
    }

    .explorer-toolbar {
      flex-direction: column;
      align-items: stretch;
    }

    .explorer-search {
      max-width: none;
    }

    .explorer-controls {
      flex-wrap: wrap;
    }

    .favorites-grid,
    .news-grid {
      grid-template-columns: 1fr;
    }

    .editorial-featured {
      grid-column: span 1;
    }

    .footer-inner {
      flex-direction: column;
      align-items: flex-start;
    }

    .footer-meta {
      text-align: left;
    }

    .stock-modal {
      padding: 18px;
    }

    .modal-price-row,
    .chart-toolbar {
      flex-wrap: wrap;
    }

    .stock-metrics {
      grid-template-columns: repeat(2, 1fr);
    }

    .chart-area svg {
      height: 190px;
    }

    .chart-legend {
      flex-wrap: wrap;
    }
  }

  @media (prefers-reduced-motion: reduce) {
    .finance-luxury,
    .finance-luxury * {
      transition: none !important;
      scroll-behavior: auto !important;
    }
  }
</style>
