<script setup>
import { ref, computed, nextTick, onMounted } from "vue";
import { Play, Star, ChevronRight, Search, Film } from "lucide-vue-next";
import Header from "../components/Header.vue";
import Footer from "../components/Footer.vue";

// In .env: VITE_API_URL=http://YOUR-LAN-IP:8082/api/movies (needed to test on a phone)
const API_URL = import.meta.env.VITE_API_URL ?? "http://localhost:8082/api/movies";

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

const byRating = (a, b) => Number(b.rating || 0) - Number(a.rating || 0);

function getPoster(movie) {
  return movie.poster || fallbackPoster;
}

function getKey(movie) {
  return movie.id ?? movie._id ?? movie.title;
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
    return [...movies.value].sort(byRating).slice(0, 10);
  }

  return movies.value.filter((movie) => matchesGenre(movie, type));
}

// Computed once per data change, only categories that have movies.
const sections = computed(() =>
  categories
    .map((category) => ({ ...category, movies: moviesFor(category.type) }))
    .filter((category) => category.movies.length),
);

const featuredMovie = computed(() => [...movies.value].sort(byRating)[0]);

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

function useFallbackPoster(event) {
  event.target.onerror = null;
  event.target.src = fallbackPoster;
}

// Called when a movie is picked in the navbar search: scroll to its card and highlight it.
async function focusMovie(movie) {
  await nextTick();
  const card = document.querySelector(
    `[data-movie="${CSS.escape(String(getKey(movie)))}"]`,
  );
  if (!card) return;
  card.scrollIntoView({ behavior: "smooth", block: "center", inline: "center" });
  card.focus({ preventScroll: true });
}

onMounted(fetchMovies);
</script>

<template>
  <!-- pb-28 keeps the footer clear of the fixed mobile bottom bar -->
  <main
    class="relative isolate min-h-screen overflow-x-clip bg-[#06111e] pb-28 text-white md:pb-0"
  >
    <!-- Page background (same look as Login.vue) -->
    <div
      class="pointer-events-none fixed inset-0 -z-30 bg-[url('/images/cambodian-festival.png')] bg-cover bg-center"
    ></div>
    <div
      class="pointer-events-none fixed inset-0 -z-20 bg-gradient-to-br from-[#020817]/95 via-[#061525]/80 to-[#03120f]/95"
    ></div>
    <div
      class="pointer-events-none fixed -left-32 top-1/4 -z-10 h-80 w-80 rounded-full bg-emerald-500/20 blur-[120px]"
    ></div>
    <div
      class="pointer-events-none fixed -bottom-32 -right-20 -z-10 h-96 w-96 rounded-full bg-cyan-500/15 blur-[120px]"
    ></div>

    <!-- Navigation (fixed top bar on desktop, bottom bar on mobile) -->
    <Header :movies="movies" @select="focusMovie" />

    <!-- Hero -->
    <section
      v-if="featuredMovie && !loading"
      class="relative isolate min-h-[480px] overflow-hidden sm:min-h-[560px]"
    >
      <img
        :src="getPoster(featuredMovie)"
        :alt="featuredMovie.title"
        class="absolute inset-0 -z-20 h-full w-full object-cover object-center opacity-30 blur-[2px]"
        @error="useFallbackPoster"
      />

      <div
        class="absolute inset-0 -z-10 bg-gradient-to-r from-[#020817] via-[#020817]/90 to-[#020817]/35"
      />
      <div
        class="absolute inset-0 -z-10 bg-gradient-to-t from-[#020817] via-transparent to-[#020817]/30"
      />

      <!-- pt-28 clears the fixed navbar -->
      <div
        class="mx-auto flex min-h-[480px] max-w-[1600px] items-center px-5 pb-16 pt-28 sm:min-h-[560px] sm:px-8 lg:px-12"
      >
        <div class="max-w-2xl">
          <div
            class="mb-5 inline-flex items-center gap-2 rounded-full border border-emerald-300/20 bg-emerald-300/10 px-4 py-2 text-xs font-semibold uppercase tracking-[0.2em] text-emerald-200"
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
            class="mb-5 flex flex-wrap items-center gap-4 text-sm text-slate-300"
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

          <p class="mb-8 max-w-xl text-sm leading-7 text-slate-300 sm:text-base">
            {{
              featuredMovie.description ||
              "Discover your next favorite movie and explore a world of cinematic adventures."
            }}
          </p>

          <a
            href="#popular"
            class="inline-flex items-center gap-2 rounded-xl bg-gradient-to-r from-emerald-300 to-teal-300 px-6 py-3.5 font-bold text-slate-950 shadow-xl shadow-emerald-500/10 transition duration-300 hover:-translate-y-0.5 hover:shadow-emerald-500/20"
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
      class="mx-auto grid max-w-[1600px] grid-cols-2 gap-5 px-5 pb-16 pt-28 sm:grid-cols-3 sm:px-8 md:grid-cols-4 lg:grid-cols-6 lg:px-12"
    >
      <div v-for="n in 6" :key="n" class="animate-pulse">
        <div class="aspect-[2/3] rounded-2xl bg-white/10" />
        <div class="mt-3 h-4 rounded bg-white/10" />
        <div class="mt-2 h-3 w-2/3 rounded bg-white/5" />
      </div>
    </div>

    <!-- Error -->
    <div
      v-else-if="error"
      class="mx-auto max-w-3xl px-5 pb-20 pt-32 text-center"
    >
      <Film :size="48" class="mx-auto mb-4 text-emerald-300" />
      <h2 class="mb-3 text-xl font-bold">Movies could not be loaded</h2>
      <p class="mb-6 text-sm leading-6 text-slate-400">{{ error }}</p>
      <button
        class="rounded-xl bg-gradient-to-r from-emerald-300 to-teal-300 px-6 py-3 font-semibold text-slate-950 transition duration-300 hover:-translate-y-0.5"
        @click="fetchMovies"
      >
        Try Again
      </button>
    </div>

    <!-- Movie categories -->
    <div
      v-else
      class="mx-auto max-w-[1600px] px-5 pb-20 sm:px-8 lg:px-12"
      :class="{ 'pt-24': !featuredMovie }"
    >
      <section
        v-for="category in sections"
        :id="category.type"
        :key="category.type"
        class="movie-section py-8 sm:py-10"
      >
        <!-- Section heading -->
        <div class="mb-6 flex items-end justify-between gap-4">
          <div>
            <p
              class="mb-2 text-[10px] font-bold uppercase tracking-[0.25em] text-emerald-300"
            >
              {{ category.type === "popular" ? "Trending now" : "Explore genre" }}
            </p>

            <h2 class="text-2xl font-black sm:text-3xl">
              {{ category.title }}
              <span class="text-emerald-300">.</span>
            </h2>

            <p class="mt-2 text-sm text-slate-500">
              {{ category.subtitle }}
            </p>
          </div>

          <div class="hidden gap-2 sm:flex">
            <button
              :aria-label="`Scroll ${category.title} left`"
              class="flex h-10 w-10 items-center justify-center rounded-full border border-white/10 bg-white/5 transition hover:border-emerald-300/50 hover:bg-emerald-400/10"
              @click="scrollSection(category.type, -1)"
            >
              <ChevronRight :size="20" class="rotate-180" />
            </button>

            <button
              :aria-label="`Scroll ${category.title} right`"
              class="flex h-10 w-10 items-center justify-center rounded-full border border-white/10 bg-white/5 transition hover:border-emerald-300/50 hover:bg-emerald-400/10"
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
            v-for="(movie, index) in category.movies"
            :key="getKey(movie)"
            :data-movie="getKey(movie)"
            tabindex="0"
            class="movie-card group w-[155px] flex-none snap-start outline-none sm:w-[190px] lg:w-[215px]"
          >
            <div
              class="relative aspect-[2/3] overflow-hidden rounded-2xl border border-white/[0.08] bg-[#061525] shadow-lg shadow-black/20 transition duration-300 group-focus-within:-translate-y-2 group-focus-within:border-emerald-300/50 group-hover:-translate-y-2 group-hover:border-emerald-300/50 group-hover:shadow-xl group-hover:shadow-emerald-950/40"
            >
              <img
                :src="getPoster(movie)"
                :alt="movie.title"
                loading="lazy"
                class="h-full w-full object-cover transition duration-500 group-focus-within:scale-110 group-hover:scale-110"
                @error="useFallbackPoster"
              />

              <div
                class="absolute inset-0 bg-gradient-to-t from-black/95 via-black/5 to-black/10 opacity-80 transition group-focus-within:opacity-100 group-hover:opacity-100"
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
                class="absolute right-3 top-3 rounded-md bg-emerald-300 px-2 py-1 text-slate-950 text-[10px] font-black uppercase tracking-wide"
              >
                Top {{ index + 1 }}
              </div>

              <!-- Details: shown on hover, keyboard focus, or tap -->
              <div
                class="absolute inset-x-0 bottom-0 translate-y-2 p-3 opacity-0 transition duration-300 group-focus-within:translate-y-0 group-focus-within:opacity-100 group-hover:translate-y-0 group-hover:opacity-100 sm:p-4"
              >
                <p class="mb-2 line-clamp-3 text-xs leading-5 text-slate-200">
                  {{
                    movie.description ||
                    "Discover this movie on Cineverse Legend."
                  }}
                </p>

                <div class="flex items-center gap-2 text-xs text-slate-300">
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
                class="truncate text-sm font-bold text-slate-100 transition group-focus-within:text-emerald-300 group-hover:text-emerald-300 sm:text-base"
                :title="movie.title"
              >
                {{ movie.title }}
              </h3>

              <div class="mt-1.5 flex items-center justify-between gap-2">
                <p class="truncate text-xs text-slate-500">
                  {{ movie.genre || "Movie" }}
                </p>
                <span class="shrink-0 text-xs text-slate-500">
                  {{ getYear(movie) }}
                </span>
              </div>
            </div>
          </article>
        </div>
      </section>

      <div v-if="!movies.length" class="py-20 text-center">
        <Search :size="40" class="mx-auto mb-4 text-slate-600" />
        <h2 class="mb-2 text-xl font-bold">No movies yet</h2>
        <p class="text-sm text-slate-500">
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

/* Clears the fixed navbar when jumping to #horror, #animation, etc. */
.movie-section {
  scroll-margin-top: 96px;
}

.movie-card {
  min-width: 0;
}

/* Tailwind 3.3+ already ships line-clamp-3; this only covers older versions. */
.line-clamp-3 {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 3;
  overflow: hidden;
}
</style>