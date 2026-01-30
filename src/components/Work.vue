<template>
  <section class="work">
    <!-- ================= GITHUB ================= -->
    <section class="github-section">
      <div class="github-repos-box">
        <h3 class="github-repos-title">Repositories</h3>
        <div
          class="github-repo-item"
          v-for="repo in github.repos"
          :key="repo.id"
          >
          <span class="repo-name">{{ repo.name }}</span>
          <div class="repo-starfork">
            <span class="repo-stars">
              <Icon icon="solar:star-bold" width="30px" height="30px" />
              {{ repo.stars }}
            </span>
            <span class="repo-forks">
              <Icon icon="flowbite:code-fork-solid" width="30px" height="30px" />
              {{ repo.forks }}
            </span>
          </div>
        </div>
      </div>
      <div class="github-information">
        <div class="github-languages-box">
          <h3 class="github-languages-title">Languages</h3>
          <ApexChart
            type="donut"
            :options="githubLanguageOptions"
            :series="githubLanguageSeries"
            />
        </div>

        <div class="github-followers-box">
          <p class="github-followers-label">
            Followers
          </p>
          <strong class="github-followers-value">{{ github.followers }}</strong>
        </div>

        <!-- Heat.js Container -->
        <div class="github-contribution-map">
          <div id="heat-map" ref="heatMapEl" class="heat-map-container"></div>
        </div>
      </div>
    </section>

    <!-- ================= ARTICLES ================= -->
    <section class="articles-section">
      <h2 class="articles-title">Articles</h2>
      <div
        class="article-item medium-article"
        v-for="article in mediumArticles"
        :key="article.link"
        >
        <span class="article-source medium-logo">
          <Icon icon="ri:medium-fill" width="30px" height="30px" />
        </span>
        <p class="article-title">
          {{ article.title }}
        </p>
      </div>
    </section>

    <!-- ================= BLOG POSTS ================= -->
    <section class="blog-posts-section">
      <h2 class="blog-posts-title">Blog Posts</h2>
      <div
        class="blog-post-item devto-post"
        v-for="post in devtoPosts"
        :key="post.id"
        >
        <span class="blog-source devto-logo">
          <Icon icon="lineicons:dev" width="30px" height="30px" />
        </span>
        <p class="blog-post-title">
          {{ post.title }}
        </p>
      </div>
    </section>

    <!-- ================= LEETCODE ================= -->
    <section class="leetcode-section">
      <h2 class="leetcode-title">LeetCode</h2>
      <div class="leetcode-summary">
        <p>
          Total Solved: <strong>{{ leetcode.total }}</strong>
        </p>
        <p>
          Rank: <strong>{{ leetcode.rank }}</strong>
        </p>
        <div class="leetcode-graphbox">
          <ApexChart
            type="donut"
            :options="leetcodeChartOptions"
            :series="leetcodeChartSeries"
            />
        </div>
      </div>
    </section>
  </section>
</template>

<script setup>
  import {
    ref,
    onMounted,
    nextTick,
    onUnmounted
  } from "vue";
  import {
    Icon
  } from "@iconify/vue";
  import ApexChart from "vue3-apexcharts";

  const GITHUB = "CaptainFulminyx";
  const DEVTO = "captainfulminyx";
  const MEDIUM = "captainfulminyx";
  const LEETCODE = "CaptainFulminyx";

  /* ================= STATE ================= */
  const github = ref( {
    followers: 0,
    repos: [],
    languages: {}
  });

  const mediumArticles = ref([]);
  const devtoPosts = ref([]);
  const leetcode = ref( {
    total: 0,
    easy: 0,
    medium: 0,
    hard: 0,
    rank: "-"
  });

  /* ================= GITHUB ================= */
  const githubLanguageOptions = ref( {
    labels: []
  });
  const githubLanguageSeries = ref([]);
  const heatMapEl = ref(null);

  async function loadGitHub() {
    try {
      const user = await fetch(
        `https://api.github.com/users/${GITHUB}`
      ).then(r => r.json());

      github.value.followers = user.followers;

      const repos = await fetch(
        `https://api.github.com/users/${GITHUB}/repos?per_page=50`
      ).then(r => r.json());

      const languageCount = {};
      const filteredRepos = [];

      repos.forEach(repo => {
        if (repo.language) {
          languageCount[repo.language] = (languageCount[repo.language] || 0) + 1;
        }

        if (repo.stargazers_count >= 2) {
          filteredRepos.push({
            id: repo.id,
            name: repo.name,
            stars: repo.stargazers_count,
            forks: repo.forks_count
          });
        }
      });

      github.value.repos = filteredRepos.slice(0,
        4);
      githubLanguageOptions.value = {
        labels: Object.keys(languageCount),
        legend: {
          show: true
        }
      };
      githubLanguageSeries.value = Object.values(languageCount);

    } catch (err) {
      console.error("GitHub load failed",
        err);
    }
  }

  async function fetchGitHubContributions(username) {
    try {
      const res = await fetch(
        `https://github-contributions-api.jogruber.de/v4/${username}?y=last`
      );

      if (!res.ok) {
        throw new Error(`HTTP error! status: ${res.status}`);
      }

      const data = await res.json();
      return data.contributions;
    } catch (err) {
      console.error("Failed to fetch GitHub contributions", err);
      return [];
    }
  }

  async function initHeatMap() {
    try {
      await nextTick(); // Wait for DOM

      if (!heatMapEl.value) {
        console.error("Heat map element not found");
        return;
      }

      // Load Heat.js dynamically
      await loadHeatJsScripts();

      // Wait a bit for Heat.js to initialize
      await new Promise(resolve => setTimeout(resolve, 100));

      // Fetch contribution data
      const contributions = await fetchGitHubContributions(GITHUB);

      if (!contributions || contributions.length === 0) {
        console.warn("No contribution data available");
        return;
      }

      // Clear existing content
      heatMapEl.value.innerHTML = '';

      // Create a new div for Heat.js
      const heatContainer = document.createElement('div');
      heatContainer.id = 'github-heat-map';
      heatContainer.setAttribute('data-heat-js', JSON.stringify({
        'views': {
          'map': {
            'showDayNames': true,
            'colorRanges': [{
              'min': 0, 'max': 0, 'color': '#ebedf0'
            },
              {
                'min': 1, 'max': 1, 'color': '#9be9a8'
              },
              {
                'min': 2, 'max': 4, 'color': '#40c463'
              },
              {
                'min': 5, 'max': 9, 'color': '#30a14e'
              },
              {
                'min': 10, 'max': 999, 'color': '#216e39'
              }]
          }
        }
      }));

      heatMapEl.value.appendChild(heatContainer);

      // Process and add dates to Heat.js
      contributions.forEach(day => {
        if (day.count > 0) {
          const date = new Date(day.date);
          // Add multiple entries for higher counts (Heat.js uses frequency)
          for (let i = 0; i < day.count; i++) {
            if (window.$heat && window.$heat.addDate) {
              window.$heat.addDate('github-heat-map', date, 'Contributions', true);
            }
          }
        }
      });

    } catch (err) {
      console.error("Failed to initialize Heat.js",
        err);
    }
  }

  function loadHeatJsScripts() {
    return new Promise((resolve, reject) => {
      // Check if already loaded
      if (window.$heat) {
        resolve();
        return;
      }

      // Load CSS
      const link = document.createElement('link');
      link.rel = 'stylesheet';
      link.href = 'https://cdn.jsdelivr.net/gh/williamtroup/Heat.js@4.5.3/dist/heat.js.min.css';
      link.onload = () => {
        // Load JS
        const script = document.createElement('script');
        script.src = 'https://cdn.jsdelivr.net/gh/williamtroup/Heat.js@4.5.3/dist/heat.min.js';
        script.onload = () => {
          // Wait for Heat.js to initialize
          const checkHeat = setInterval(() => {
            if (window.$heat) {
              clearInterval(checkHeat);
              resolve();
            }
          },
            50);

          // Timeout after 5 seconds
          setTimeout(() => {
            clearInterval(checkHeat);
            if (!window.$heat) {
              reject(new Error('Heat.js failed to load'));
            }
          },
            5000);
        };
        script.onerror = reject;
        document.head.appendChild(script);
      };
      link.onerror = reject;
      document.head.appendChild(link);
    });
  }

  /* ================= DEV.TO ================= */
  async function loadDevto() {
    try {
      const posts = await fetch(
        `https://dev.to/api/articles?username=${DEVTO}`
      ).then(r => r.json());

      devtoPosts.value = posts.slice(0, 4).map(post => ({
        id: post.id,
        title: post.title
      }));
    } catch (err) {
      console.error("Dev.to load failed", err);
    }
  }

  /* ================= MEDIUM ================= */
  async function loadMedium() {
    try {
      const rss = `https://medium.com/feed/@${MEDIUM}`;
      const data = await fetch(
        `https://api.rss2json.com/v1/api.json?rss_url=${encodeURIComponent(rss)}`
      ).then(r => r.json());

      mediumArticles.value = data.items.slice(0, 4).map(item => ({
        title: item.title,
        link: item.link
      }));
    } catch (err) {
      console.error("Medium load failed", err);
    }
  }

  /* ================= LEETCODE ================= */
  const leetcodeChartOptions = ref( {
    labels: ["Easy", "Medium", "Hard"],
    legend: {
      show: true
    }
  });

  const leetcodeChartSeries = ref([]);

  async function loadLeetCode() {
    try {
      const data = await fetch(
        `https://leetcode-stats-api.herokuapp.com/${LEETCODE}`
      ).then(r => r.json());

      leetcode.value.total = data.totalSolved;
      leetcode.value.easy = data.easySolved;
      leetcode.value.medium = data.mediumSolved;
      leetcode.value.hard = data.hardSolved;
      leetcode.value.rank = data.ranking ? `#${data.ranking}`: "-";

      leetcodeChartSeries.value = [
        data.easySolved,
        data.mediumSolved,
        data.hardSolved
      ];
    } catch (err) {
      console.error("LeetCode load failed", err);
    }
  }

  /* ================= INIT ================= */
  onMounted(async () => {
    try {
      await Promise.all([
        loadGitHub(),
        loadDevto(),
        loadMedium(),
        loadLeetCode()
      ]);

      // Initialize Heat.js after data is loaded
      await initHeatMap();

    } catch (err) {
      console.error("Failed to initialize components",
        err);
    }
  });

  // Clean up if needed
  onUnmounted(() => {
    // Heat.js doesn't have a destroy method in the public API
    // but we can clean up the DOM
    if (heatMapEl.value) {
      heatMapEl.value.innerHTML = '';
    }
  });
</script>

<style>
  /* Heat.js Container Styling */
  .heat-map-container {
    width: 100%;
    min-height: 180px;
    overflow: auto;
    margin-top: 20px;
    background: #fff;
    border-radius: 8px;
    padding: 10px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  }

  /* Ensure Heat.js displays properly */
  [data-heat-js] {
    width: 100% !important;
    height: auto !important;
  }

  /* Mobile responsiveness */
  @media (max-width: 768px) {
    .heat-map-container {
      min-height: 150px;
      padding: 8px;
    }
  }
</style>