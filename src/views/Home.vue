<script setup>
import { ref, computed, onMounted } from "vue";
import { Play, Star, ChevronRight, Search, Film } from "lucide-vue-next";
import Header from "../components/Header.vue";
import Footer from "../components/Footer.vue";

const API_URL = "http://localhost:8082/api/movies";

const movies = ref([]);
const loading = ref(true);
const error = ref("");

const categories = [
  {
    title: "Popular Movies",
    subtitle: "Movies everyone is talking about",
    type: "popular",
  },
  { title: "Horror", subtitle: "Dare to watch?", type: "horror" },
  {
    title: "Animation & Cartoon",
    subtitle: "Fun for everyone",
    type: "animation",
  },
  {
    title: "Action & Adventure",
    subtitle: "Full of excitement",
    type: "action",
  },
  { title: "Sci-Fi", subtitle: "Explore another universe", type: "sci-fi" },
];

const fallbackPoster =
  "https://placehold.co/500x750/111827/FFFFFF?text=No+Poster";

const normalize = (value) =>
  String(value || "")
    .toLowerCase()
    .trim();

function getPoster(movie) {
  return movie.poster || fallbackPoster;
}

function getYear(movie) {
  if (!movie.release_date) return "Coming soon";
  return String(movie.release_date).slice(0, 4);
}

function getRating(movie) {
  const rating = Number(movie.rating);
  return Number.isFinite(rating) && rating > 0 ? rating.toFixed(1) : "N/A";
}

function matchesGenre(movie, type) {
  const genre = normalize(movie.genre);

  if (type === "horror") return genre.includes("horror");

  if (type === "animation") {
    return (
      genre.includes("animation") ||
      genre.includes("cartoon") ||
      genre.includes("family")
    );
  }

  if (type === "action") {
    return genre.includes("action") || genre.includes("adventure");
  }

  if (type === "sci-fi") {
    return (
      genre.includes("sci-fi") ||
      genre.includes("sci fi") ||
      genre.includes("science fiction")
    );
  }

  return false;
}

function moviesFor(type) {
  if (type === "popular") {
    return [...movies.value]
      .sort((a, b) => Number(b.rating || 0) - Number(a.rating || 0))
      .slice(0, 10);
  }

  return movies.value.filter((movie) => matchesGenre(movie, type));
}

const featuredMovie = computed(() => {
  return [...movies.value].sort(
    (a, b) => Number(b.rating || 0) - Number(a.rating || 0),
  )[0];
});

async function fetchMovies() {
  loading.value = true;
  error.value = "";

  try {
    const response = await fetch(API_URL);

    if (!response.ok) {
      throw new Error(`Server error: ${response.status}`);
    }

    const result = await response.json();

    // Supports common Express response formats.
    const list = Array.isArray(result)
      ? result
      : Array.isArray(result.movies)
        ? result.movies
        : Array.isArray(result.data)
          ? result.data
          : [];

    movies.value = list;
  } catch (err) {
    error.value =
      "Unable to load movies. Please check that your Express server is running and the API URL is correct.";
    console.error("Failed to fetch movies:", err);
  } finally {
    loading.value = false;
  }
}

function scrollSection(type, direction) {
  const element = document.getElementById(`movie-row-${type}`);

  if (element) {
    element.scrollBy({
      left: direction * element.clientWidth * 0.8,
      behavior: "smooth",
    });
  }
}

onMounted(fetchMovies);
</script>

<template>
  <main class="min-h-screen overflow-hidden bg-[#080b12] text-white">
    <!-- Navigation -->
    <header
      class="sticky top-0 z-40 border-b border-white/10 bg-[#080b12]/85 backdrop-blur-xl"
    >
      <Header />
    </header>
    <!-- Hero -->
    <section
      v-if="featuredMovie && !loading"
      class="relative isolate min-h-[480px] overflow-hidden sm:min-h-[560px]"
    >
      <img
        :src="getPoster(featuredMovie)"
        :alt="featuredMovie.title"
        class="absolute inset-0 -z-20 h-full w-full object-cover object-center opacity-30 blur-[2px]"
      />

      <div
        class="absolute inset-0 -z-10 bg-gradient-to-r from-[#080b12] via-[#080b12]/90 to-[#080b12]/35"
      />
      <div
        class="absolute inset-0 -z-10 bg-gradient-to-t from-[#080b12] via-transparent to-[#080b12]/30"
      />

      <div
        class="mx-auto flex min-h-[480px] max-w-[1600px] items-center px-5 py-16 sm:min-h-[560px] sm:px-8 lg:px-12"
      >
        <div class="max-w-2xl">
          <div
            class="mb-5 inline-flex items-center gap-2 rounded-full border border-violet-400/30 bg-violet-500/10 px-4 py-2 text-xs font-semibold uppercase tracking-[0.2em] text-violet-300"
          >
            <Star :size="14" class="fill-current" />
            Featured movie
          </div>

          <h2
            class="mb-5 text-4xl font-black leading-tight tracking-tight sm:text-6xl lg:text-7xl"
          >
            {{ featuredMovie.title }}
          </h2>

          <div
            class="mb-5 flex flex-wrap items-center gap-4 text-sm text-gray-300"
          >
            <span class="flex items-center gap-1.5 text-yellow-400">
              <Star :size="16" class="fill-current" />
              {{ getRating(featuredMovie) }}
            </span>
            <span>{{ getYear(featuredMovie) }}</span>
            <span>{{ featuredMovie.genre || "Movie" }}</span>
            <span v-if="featuredMovie.duration">
              {{ featuredMovie.duration }} min
            </span>
          </div>

          <p class="mb-8 max-w-xl text-sm leading-7 text-gray-300 sm:text-base">
            {{
              featuredMovie.description ||
              "Discover your next favorite movie and explore a world of cinematic adventures."
            }}
          </p>

          <a
            href="#popular"
            class="inline-flex items-center gap-2 rounded-xl bg-violet-600 px-6 py-3.5 font-bold shadow-xl shadow-violet-600/20 transition hover:-translate-y-0.5 hover:bg-violet-500"
          >
            <Play :size="18" class="fill-current" />
            Explore Movies
          </a>
        </div>
      </div>
    </section>

    <!-- Loading -->
    <div
      v-if="loading"
      class="mx-auto grid max-w-[1600px] grid-cols-2 gap-5 px-5 py-16 sm:grid-cols-3 sm:px-8 md:grid-cols-4 lg:grid-cols-6 lg:px-12"
    >
      <div v-for="n in 6" :key="n" class="animate-pulse">
        <div class="aspect-[2/3] rounded-2xl bg-white/10" />
        <div class="mt-3 h-4 rounded bg-white/10" />
        <div class="mt-2 h-3 w-2/3 rounded bg-white/5" />
      </div>
    </div>

    <!-- Error -->
    <div v-else-if="error" class="mx-auto max-w-3xl px-5 py-20 text-center">
      <Film :size="48" class="mx-auto mb-4 text-violet-400" />
      <h2 class="mb-3 text-xl font-bold">Movies could not be loaded</h2>
      <p class="mb-6 text-sm leading-6 text-gray-400">{{ error }}</p>
      <button
        class="rounded-xl bg-violet-600 px-6 py-3 font-semibold hover:bg-violet-500"
        @click="fetchMovies"
      >
        Try Again
      </button>
    </div>

    <!-- Movie categories -->
    <div v-else class="mx-auto max-w-[1600px] px-5 pb-20 sm:px-8 lg:px-12">
      <section
        v-for="category in categories"
        :id="category.type"
        :key="category.type"
        class="movie-section py-8 sm:py-10"
      >
        <template v-if="moviesFor(category.type).length">
          <!-- Section heading -->
          <div class="mb-6 flex items-end justify-between gap-4">
            <div>
              <p
                class="mb-2 text-[10px] font-bold uppercase tracking-[0.25em] text-violet-400"
              >
                {{
                  category.type === "popular" ? "Trending now" : "Explore genre"
                }}
              </p>

              <h2 class="text-2xl font-black sm:text-3xl">
                {{ category.title }}
                <span class="text-violet-400">.</span>
              </h2>

              <p class="mt-2 text-sm text-gray-500">
                {{ category.subtitle }}
              </p>
            </div>

            <div class="hidden gap-2 sm:flex">
              <button
                :aria-label="`Scroll ${category.title} left`"
                class="flex h-10 w-10 items-center justify-center rounded-full border border-white/10 bg-white/5 transition hover:border-violet-400 hover:bg-violet-600"
                @click="scrollSection(category.type, -1)"
              >
                <ChevronRight :size="20" class="rotate-180" />
              </button>

              <button
                :aria-label="`Scroll ${category.title} right`"
                class="flex h-10 w-10 items-center justify-center rounded-full border border-white/10 bg-white/5 transition hover:border-violet-400 hover:bg-violet-600"
                @click="scrollSection(category.type, 1)"
              >
                <ChevronRight :size="20" />
              </button>
            </div>
          </div>

          <!-- One horizontal row per category -->
          <div
            :id="`movie-row-${category.type}`"
            class="movie-row flex snap-x snap-mandatory gap-4 overflow-x-auto pb-4 sm:gap-5"
          >
            <article
              v-for="(movie, index) in moviesFor(category.type)"
              :key="movie.id ?? movie.title"
              class="movie-card group w-[155px] flex-none snap-start sm:w-[190px] lg:w-[215px]"
            >
              <div
                class="relative aspect-[2/3] overflow-hidden rounded-2xl border border-white/[0.08] bg-[#111521] shadow-lg shadow-black/20 transition duration-300 group-hover:-translate-y-2 group-hover:border-violet-400/50 group-hover:shadow-xl group-hover:shadow-violet-950/40"
              >
                <img
                  :src="getPoster(movie)"
                  :alt="movie.title"
                  loading="lazy"
                  class="h-full w-full object-cover transition duration-500 group-hover:scale-110"
                  @error="
                    (event) => {
                      event.target.onerror = null;
                      event.target.src = fallbackPoster;
                    }
                  "
                />

                <div
                  class="absolute inset-0 bg-gradient-to-t from-black/95 via-black/5 to-black/10 opacity-80 transition group-hover:opacity-100"
                />

                <div
                  class="absolute left-3 top-3 rounded-lg border border-white/10 bg-black/60 px-2.5 py-1.5 text-xs font-bold backdrop-blur-md"
                >
                  <span class="flex items-center gap-1 text-yellow-400">
                    <Star :size="12" class="fill-current" />
                    {{ getRating(movie) }}
                  </span>
                </div>

                <div
                  v-if="category.type === 'popular' && index < 3"
                  class="absolute right-3 top-3 rounded-md bg-violet-600 px-2 py-1 text-[10px] font-black uppercase tracking-wide"
                >
                  Top {{ index + 1 }}
                </div>

                <div
                  class="absolute inset-x-0 bottom-0 translate-y-2 p-3 opacity-0 transition duration-300 group-hover:translate-y-0 group-hover:opacity-100 sm:p-4"
                >
                  <p class="mb-2 line-clamp-3 text-xs leading-5 text-gray-200">
                    {{
                      movie.description ||
                      "Discover this movie on Cineverse Legend."
                    }}
                  </p>

                  <div class="flex items-center gap-2 text-xs text-gray-300">
                    <span>{{
                      movie.duration ? `${movie.duration} min` : "Movie"
                    }}</span>
                    <span>•</span>
                    <span>{{ getYear(movie) }}</span>
                  </div>
                </div>
              </div>

              <div class="px-1 pt-3">
                <h3
                  class="truncate text-sm font-bold text-gray-100 transition group-hover:text-violet-300 sm:text-base"
                  :title="movie.title"
                >
                  {{ movie.title }}
                </h3>

                <div class="mt-1.5 flex items-center justify-between gap-2">
                  <p class="truncate text-xs text-gray-500">
                    {{ movie.genre || "Movie" }}
                  </p>
                  <span class="shrink-0 text-xs text-gray-500">
                    {{ getYear(movie) }}
                  </span>
                </div>
              </div>
            </article>
          </div>
        </template>
      </section>

      <div v-if="!movies.length" class="py-20 text-center">
        <Search :size="40" class="mx-auto mb-4 text-gray-600" />
        <h2 class="mb-2 text-xl font-bold">No movies yet</h2>
        <p class="text-sm text-gray-500">
          Add some movies to your database to see them here.
        </p>
      </div>
    </div>

    <!-- Footer -->
    <Footer />
  </main>
</template>

<style scoped>
.movie-row {
  scrollbar-width: none;
  -ms-overflow-style: none;
  overscroll-behavior-x: contain;
}

.movie-row::-webkit-scrollbar {
  display: none;
  width: 0;
  height: 0;
}

.movie-row::-webkit-scrollbar-thumb {
  background: transparent;
}

.movie-section {
  scroll-margin-top: 90px;
}

.movie-card {
  min-width: 0;
}

.line-clamp-3 {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3;
  overflow: hidden;
}
</style>
