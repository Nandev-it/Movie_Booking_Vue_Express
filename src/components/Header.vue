<script setup>
import { ref, computed, watch, nextTick, onMounted, onBeforeUnmount } from 'vue'
import { RouterLink } from 'vue-router'
import { Clapperboard } from 'lucide-vue-next'

const props = defineProps({
  userName: { type: String, default: 'Sarah' },
  avatar: { type: String, default: '' },
  hasNotification: { type: Boolean, default: true },
  // Movies to search through (title, genre, poster, rating, release_date)
  movies: { type: Array, default: () => [] },
})
const emit = defineEmits(['notifications', 'profile', 'select'])

// Icon paths (Heroicons outline, 24x24)
const icons = {
  home: 'M2.25 12l8.954-8.955a1.126 1.126 0 011.591 0L21.75 12M4.5 9.75v10.125c0 .621.504 1.125 1.125 1.125H9.75v-4.875c0-.621.504-1.125 1.125-1.125h2.25c.621 0 1.125.504 1.125 1.125V21h4.125c.621 0 1.125-.504 1.125-1.125V9.75M8.25 21h8.25',
  horror: 'M21.752 15.002A9.718 9.718 0 0118 15.75c-5.385 0-9.75-4.365-9.75-9.75 0-1.33.266-2.597.748-3.752A9.753 9.753 0 003 11.25C3 16.635 7.365 21 12.75 21a9.753 9.753 0 009.002-5.998z',
  animation: 'M9.813 15.904L9 18.75l-.813-2.846a4.5 4.5 0 00-3.09-3.09L2.25 12l2.846-.813a4.5 4.5 0 003.09-3.09L9 5.25l.813 2.846a4.5 4.5 0 003.09 3.09L15.75 12l-2.846.813a4.5 4.5 0 00-3.09 3.09zM18.259 8.715L18 9.75l-.259-1.035a3.375 3.375 0 00-2.455-2.456L14.25 6l1.036-.259a3.375 3.375 0 002.455-2.456L18 2.25l.259 1.035a3.375 3.375 0 002.456 2.456L21.75 6l-1.035.259a3.375 3.375 0 00-2.456 2.456z',
  action: 'M3.75 13.5l10.5-11.25L12 10.5h8.25L9.75 21.75 12 13.5H3.75z',
  more: 'M6.75 12a.75.75 0 11-1.5 0 .75.75 0 011.5 0zM12.75 12a.75.75 0 11-1.5 0 .75.75 0 011.5 0zM18.75 12a.75.75 0 11-1.5 0 .75.75 0 011.5 0z',
  search: 'M21 21l-5.197-5.197m0 0A7.5 7.5 0 105.196 5.196a7.5 7.5 0 0010.607 10.607z',
  bell: 'M14.857 17.082a23.848 23.848 0 005.454-1.31A8.967 8.967 0 0118 9.75v-.7V9A6 6 0 006 9v.75a8.967 8.967 0 01-2.312 6.022c1.733.64 3.56 1.085 5.455 1.31m5.714 0a24.255 24.255 0 01-5.714 0m5.714 0a3 3 0 11-5.714 0',
  chevron: 'M19.5 8.25l-7.5 7.5-7.5-7.5',
  close: 'M6 18L18 6M6 6l12 12',
}

// Same links on desktop (top bar) and mobile (bottom bar)
const links = [
  { key: 'home', label: 'Home', to: '/' },
  { key: 'horror', label: 'Horror', href: '#horror' },
  { key: 'animation', label: 'Animation', href: '#animation' },
  { key: 'action', label: 'Action', href: '#action' },
  { key: 'more', label: 'More', to: '/more' },
]

const active = ref('home')
const scrolled = ref(false)

const onScroll = () => {
  scrolled.value = window.scrollY > 24
}
const syncFromHash = () => {
  const key = window.location.hash.replace('#', '')
  active.value = links.some((l) => l.key === key) ? key : 'home'
}

/* ---------------- Search ---------------- */
const query = ref('')
const focused = ref(false) // input has focus
const panelOpen = ref(false) // results / suggestions panel
const mobileOpen = ref(false) // search bar open on small screens
const activeIndex = ref(-1)
const inputRef = ref(null)
const searchRoot = ref(null)

const norm = (v) => String(v || '').toLowerCase().trim()
const keyOf = (m) => m.id ?? m._id ?? m.title
const yearOf = (m) => (m.release_date ? String(m.release_date).slice(0, 4) : '')
const ratingOf = (m) => {
  const r = Number(m.rating)
  return Number.isFinite(r) && r > 0 ? r.toFixed(1) : ''
}

// Search by title or genre. Title matches rank above genre matches.
const results = computed(() => {
  const q = norm(query.value)
  if (!q) return []
  return props.movies
    .map((m) => {
      const title = norm(m.title)
      const genre = norm(m.genre)
      const score = title.startsWith(q) ? 3 : title.includes(q) ? 2 : genre.includes(q) ? 1 : 0
      return { m, score }
    })
    .filter((r) => r.score)
    .sort((a, b) => b.score - a.score || Number(b.m.rating || 0) - Number(a.m.rating || 0))
    .slice(0, 8)
    .map((r) => r.m)
})

// Most common genres in the data, used for suggestion chips
const genres = computed(() => {
  const counts = new Map()
  props.movies.forEach((m) =>
    String(m.genre || '')
      .split(/[,/|]/)
      .map((g) => g.trim())
      .filter(Boolean)
      .forEach((g) => {
        const k = g.toLowerCase()
        counts.set(k, { name: g, n: (counts.get(k)?.n || 0) + 1 })
      }),
  )
  const list = [...counts.values()].sort((a, b) => b.n - a.n).slice(0, 6).map((g) => g.name)
  return list.length ? list : ['Horror', 'Animation', 'Action', 'Sci-Fi']
})

function pick(movie) {
  emit('select', movie)
  closeSearch()
}
function closeSearch() {
  query.value = ''
  panelOpen.value = false
  mobileOpen.value = false
  inputRef.value?.blur()
}
function openMobile() {
  mobileOpen.value = true
  panelOpen.value = true
  nextTick(() => inputRef.value?.focus())
}
function onFieldButton() {
  if (mobileOpen.value) return closeSearch()
  query.value = ''
  inputRef.value?.focus()
}
function useGenre(name) {
  query.value = name
  inputRef.value?.focus()
}
function onKeydown(e) {
  const n = results.value.length
  if (e.key === 'ArrowDown' && n) {
    e.preventDefault()
    activeIndex.value = (activeIndex.value + 1) % n
  } else if (e.key === 'ArrowUp' && n) {
    e.preventDefault()
    activeIndex.value = (activeIndex.value - 1 + n) % n
  } else if (e.key === 'Enter' && n) {
    e.preventDefault()
    pick(results.value[activeIndex.value >= 0 ? activeIndex.value : 0])
  } else if (e.key === 'Escape') {
    closeSearch()
  }
}
function onDocPointer(e) {
  if (searchRoot.value && !searchRoot.value.contains(e.target)) {
    panelOpen.value = false
    mobileOpen.value = false
  }
}
watch(query, () => {
  activeIndex.value = -1
  panelOpen.value = true
})

/* ---------------- Typing placeholder ---------------- */
// Alternates movie names and genres, e.g. "Search Inception" -> "Search Horror"
const typingPhrases = computed(() => {
  const titles = [...new Set(props.movies.map((m) => m.title).filter(Boolean))].slice(0, 6)
  const out = []
  titles.forEach((t, i) => {
    out.push(t)
    if (genres.value[i]) out.push(genres.value[i])
  })
  return out.length ? out : ['a movie name', 'a genre like Horror']
})

// Animate when the box is empty and not focused (and always on the mobile bar)
const showTyping = computed(() => !query.value && (!focused.value || mobileOpen.value))
const typed = ref('')
let timer = null
let phraseIndex = 0
let charCount = 0
let deleting = false

function tick() {
  if (!showTyping.value) {
    timer = setTimeout(tick, 400)
    return
  }
  const phrases = typingPhrases.value
  const word = phrases[phraseIndex % phrases.length] || ''
  let delay
  if (!deleting) {
    charCount++
    typed.value = word.slice(0, charCount)
    if (charCount >= word.length) {
      deleting = true
      delay = 1600
    } else {
      delay = 70 + Math.random() * 60
    }
  } else {
    charCount--
    typed.value = word.slice(0, charCount)
    if (charCount <= 0) {
      deleting = false
      phraseIndex++
      delay = 350
    } else {
      delay = 35
    }
  }
  timer = setTimeout(tick, delay)
}

watch(typingPhrases, () => {
  phraseIndex = 0
  charCount = 0
  deleting = false
  typed.value = ''
})

onMounted(() => {
  onScroll()
  syncFromHash()
  window.addEventListener('scroll', onScroll, { passive: true })
  window.addEventListener('hashchange', syncFromHash)
  document.addEventListener('pointerdown', onDocPointer)

  if (window.matchMedia('(prefers-reduced-motion: reduce)').matches) {
    typed.value = typingPhrases.value[0]
  } else {
    tick()
  }
})
onBeforeUnmount(() => {
  clearTimeout(timer)
  window.removeEventListener('scroll', onScroll)
  window.removeEventListener('hashchange', syncFromHash)
  document.removeEventListener('pointerdown', onDocPointer)
})
</script>

<template>
  <!-- ============ TOP BAR (all sizes) ============ -->
  <header class="nav-enter fixed inset-x-0 top-0 z-50 px-3 pt-3 md:px-6 md:pt-4">
    <nav
      aria-label="Main"
      class="relative isolate mx-auto flex max-w-6xl items-center justify-between rounded-full border px-4 py-2.5 transition-all duration-500 ease-out md:px-6"
      :class="
        scrolled
          ? 'border-white/10 shadow-2xl shadow-black/50 backdrop-blur-2xl'
          : 'border-white/5 shadow-none backdrop-blur-md'
      "
    >
      <!-- Gradient surface (opacity animates because gradients can't transition) -->
      <span
        aria-hidden="true"
        class="pointer-events-none absolute inset-0 -z-10 rounded-full bg-gradient-to-br from-[#020817]/95 via-[#061525]/80 to-[#03120f]/95 transition-opacity duration-500 ease-out"
        :class="scrolled ? 'opacity-100' : 'opacity-50'"
      />
      <!-- Logo -->
      <RouterLink to="/" class="group flex items-center gap-3" aria-label="Cineverse Legend home">
        <span
          class="rounded-xl border border-emerald-300/25 bg-emerald-400/10 p-2 text-emerald-300 transition duration-300 group-hover:rotate-6 group-hover:border-emerald-300/50 group-hover:bg-emerald-400/20"
        >
          <Clapperboard :size="20" />
        </span>
        <span class="leading-none">
          <span class="block text-sm font-black tracking-[0.18em] text-white">CINEVERSE</span>
          <span class="mt-1 block text-[9px] font-bold tracking-[0.4em] text-emerald-300">LEGEND</span>
        </span>
      </RouterLink>

      <!-- Navigation links (desktop only) -->
      <div class="hidden items-center gap-8 text-sm md:flex">
        <component
          :is="link.to ? RouterLink : 'a'"
          v-for="link in links"
          :key="link.key"
          v-bind="link.to ? { to: link.to } : { href: link.href }"
          class="group relative py-1 transition-colors duration-300"
          :class="active === link.key ? 'text-emerald-300' : 'text-slate-400 hover:text-white'"
          :aria-current="active === link.key ? 'page' : undefined"
          @click="active = link.key"
        >
          {{ link.label }}
          <span
            class="absolute inset-x-0 -bottom-0.5 h-px origin-center bg-current transition-transform duration-300 ease-out"
            :class="active === link.key ? 'scale-x-100' : 'scale-x-0 group-hover:scale-x-100'"
          />
        </component>
      </div>

      <!-- Right actions -->
      <div class="flex items-center gap-1 sm:gap-2">
        <!-- Search icon (opens the search bar below xl screens) -->
        <div class="xl:hidden">
          <button
            type="button"
            class="icon-btn"
            aria-label="Search movies"
            :aria-expanded="mobileOpen"
            @click="openMobile"
          >
            <svg viewBox="0 0 24 24" class="h-5 w-5" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path :d="icons.search" /></svg>
          </button>
        </div>

        <!-- Search box: always visible on xl, covers the bar when opened on smaller screens -->
        <div
          ref="searchRoot"
          role="search"
          :class="
            mobileOpen
              ? 'absolute inset-0 z-10 flex items-center rounded-full bg-gradient-to-br from-[#020817] via-[#061525] to-[#03120f] px-3 xl:relative xl:inset-auto xl:z-auto xl:block xl:h-9 xl:w-56 xl:bg-none xl:px-0'
              : 'hidden xl:relative xl:block xl:h-9 xl:w-56'
          "
        >
          <div
            class="relative flex h-10 w-full items-center xl:absolute xl:right-0 xl:top-0 xl:h-9 xl:w-56 xl:rounded-full xl:border xl:border-white/10 xl:bg-white/5 xl:transition-all xl:duration-500 xl:ease-out xl:focus-within:w-72 xl:focus-within:border-emerald-400/70 xl:focus-within:ring-4 xl:focus-within:ring-emerald-400/10 xl:focus-within:bg-[#061525]"
          >
            <svg viewBox="0 0 24 24" class="pointer-events-none absolute left-3 h-4 w-4 text-slate-400" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path :d="icons.search" /></svg>

            <input
              ref="inputRef"
              v-model="query"
              type="text"
              role="combobox"
              autocomplete="off"
              spellcheck="false"
              aria-label="Search movies by name or genre"
              aria-controls="search-results"
              aria-autocomplete="list"
              :aria-expanded="panelOpen"
              :placeholder="focused && !showTyping ? 'Type a movie name or genre' : ''"
              class="h-full w-full bg-transparent pl-9 pr-9 text-sm text-white outline-none placeholder:text-slate-500"
              @focus="focused = true; panelOpen = true"
              @blur="focused = false"
              @keydown="onKeydown"
            />

            <!-- Animated typing placeholder -->
            <span
              v-if="showTyping"
              class="pointer-events-none absolute left-9 right-9 top-1/2 -translate-y-1/2 select-none truncate text-sm text-slate-500"
              aria-hidden="true"
            >
              Search <span class="text-slate-300">{{ typed }}</span><span v-if="!focused" class="caret" />
            </span>

            <!-- Clear (xl) / close (mobile) -->
            <button
              v-if="query || mobileOpen"
              type="button"
              class="absolute right-2 grid h-6 w-6 place-items-center rounded-full text-slate-400 transition hover:bg-white/10 hover:text-white"
              :aria-label="mobileOpen ? 'Close search' : 'Clear search'"
              @click="onFieldButton"
            >
              <svg viewBox="0 0 24 24" class="h-4 w-4" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path :d="icons.close" /></svg>
            </button>
          </div>

          <!-- Results / suggestions -->
          <Transition
            enter-active-class="transition duration-200 ease-out"
            enter-from-class="opacity-0 -translate-y-2"
            leave-active-class="transition duration-150 ease-in"
            leave-to-class="opacity-0 -translate-y-2"
          >
            <div
              v-if="panelOpen"
              class="absolute left-0 right-0 top-full z-20 mt-2 max-h-[60vh] overflow-y-auto rounded-3xl border border-white/10 bg-gradient-to-br from-[#020817]/95 via-[#061525]/80 to-[#03120f]/95 p-2 shadow-2xl shadow-black/60 backdrop-blur-2xl xl:left-auto xl:w-96"
            >
              <!-- Empty box: genre suggestions -->
              <div v-if="!query" class="p-2">
                <p class="mb-2 text-xs text-slate-400">Search by name, or pick a genre</p>
                <div class="flex flex-wrap gap-2">
                  <button
                    v-for="g in genres"
                    :key="g"
                    type="button"
                    class="rounded-full border border-white/10 bg-white/5 px-3 py-1.5 text-xs text-slate-200 transition duration-200 hover:border-emerald-400/60 hover:bg-emerald-400/15 hover:text-emerald-300 active:scale-95"
                    @mousedown.prevent
                    @click="useGenre(g)"
                  >
                    {{ g }}
                  </button>
                </div>
              </div>

              <!-- Matches -->
              <ul v-else-if="results.length" id="search-results" role="listbox">
                <li
                  v-for="(m, i) in results"
                  :key="keyOf(m)"
                  role="option"
                  :aria-selected="i === activeIndex"
                  class="flex cursor-pointer items-center gap-3 rounded-2xl p-2 transition-colors duration-150"
                  :class="i === activeIndex ? 'bg-white/10' : ''"
                  @mouseenter="activeIndex = i"
                  @mousedown.prevent
                  @click="pick(m)"
                >
                  <img
                    v-if="m.poster"
                    :src="m.poster"
                    alt=""
                    loading="lazy"
                    class="h-14 w-10 flex-none rounded-md bg-white/10 object-cover"
                    @error="(e) => (e.target.style.visibility = 'hidden')"
                  />
                  <div v-else class="h-14 w-10 flex-none rounded-md bg-white/10" />

                  <div class="min-w-0 flex-1">
                    <p class="truncate text-sm font-medium text-white">{{ m.title }}</p>
                    <p class="truncate text-xs text-slate-400">{{ m.genre || 'Movie' }}</p>
                  </div>

                  <div class="flex-none text-right text-xs">
                    <p v-if="ratingOf(m)" class="text-yellow-400">★ {{ ratingOf(m) }}</p>
                    <p class="text-slate-500">{{ yearOf(m) }}</p>
                  </div>
                </li>
              </ul>

              <!-- No matches -->
              <p v-else class="px-3 py-6 text-center text-sm text-slate-400">
                No movies found for “{{ query }}”. Try another name or genre.
              </p>
            </div>
          </Transition>
        </div>

        <button
          type="button"
          class="icon-btn relative"
          aria-label="Notifications"
          @click="$emit('notifications')"
        >
          <svg viewBox="0 0 24 24" class="h-5 w-5" fill="none" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"><path :d="icons.bell" /></svg>
          <span v-if="hasNotification" class="absolute right-1.5 top-1.5 flex h-2 w-2">
            <span class="absolute inline-flex h-full w-full animate-ping rounded-full bg-emerald-400 opacity-60 motion-reduce:animate-none" />
            <span class="relative inline-flex h-2 w-2 rounded-full bg-emerald-400" />
          </span>
        </button>

        <button
          type="button"
          class="group ml-1 flex items-center gap-2 rounded-full py-1 pl-1 pr-2 transition-colors duration-300 hover:bg-white/10"
          :aria-label="`Open profile menu for ${userName}`"
          @click="$emit('profile')"
        >
          <img
            v-if="avatar"
            :src="avatar"
            :alt="userName"
            class="h-8 w-8 rounded-full object-cover ring-2 ring-white/10 transition duration-300 group-hover:ring-emerald-400/60"
          />
          <span
            v-else
            class="grid h-8 w-8 place-items-center rounded-full bg-emerald-400/20 text-sm font-semibold text-emerald-300 ring-2 ring-white/10 transition duration-300 group-hover:ring-emerald-400/60"
          >
            {{ userName.charAt(0) }}
          </span>
          <span class="hidden text-sm text-slate-200 sm:inline">{{ userName }}</span>
          <svg viewBox="0 0 24 24" class="hidden h-3.5 w-3.5 text-slate-400 transition duration-300 group-hover:translate-y-0.5 sm:block" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path :d="icons.chevron" /></svg>
        </button>
      </div>
    </nav>
  </header>

  <!-- ============ BOTTOM BAR (mobile only) ============ -->
  <nav
    aria-label="Mobile"
    class="fixed inset-x-0 bottom-0 z-50 px-3 pb-[max(0.75rem,env(safe-area-inset-bottom))] md:hidden"
  >
    <ul
      class="relative isolate mx-auto grid max-w-md grid-cols-5 rounded-full border p-1.5 shadow-2xl shadow-black/60 backdrop-blur-2xl transition-all duration-500 ease-out"
      :class="scrolled ? 'border-white/10' : 'border-white/5'"
    >
      <li
        aria-hidden="true"
        class="pointer-events-none absolute inset-0 -z-10 rounded-full bg-gradient-to-br from-[#020817]/95 via-[#061525]/80 to-[#03120f]/95 transition-opacity duration-500 ease-out"
        :class="scrolled ? 'opacity-100' : 'opacity-70'"
      />
      <li v-for="link in links" :key="link.key">
        <component
          :is="link.to ? RouterLink : 'a'"
          v-bind="link.to ? { to: link.to } : { href: link.href }"
          class="flex flex-col items-center gap-0.5 rounded-full py-2 text-xs transition-all duration-300 active:scale-90"
          :class="active === link.key ? 'bg-emerald-400/15 font-medium text-emerald-300' : 'text-slate-400'"
          :aria-current="active === link.key ? 'page' : undefined"
          @click="active = link.key"
        >
          <svg
            viewBox="0 0 24 24"
            class="h-5 w-5 transition-transform duration-300"
            :class="active === link.key ? '-translate-y-0.5 scale-110' : ''"
            fill="none" stroke="currentColor" stroke-width="1.7" stroke-linecap="round" stroke-linejoin="round"
          >
            <path :d="icons[link.key]" />
          </svg>
          {{ link.label }}
        </component>
      </li>
    </ul>
  </nav>
</template>

<style scoped>
.icon-btn {
  display: grid;
  height: 2.25rem;
  width: 2.25rem;
  place-items: center;
  border-radius: 9999px;
  color: rgb(148 163 184);
  transition: color 0.3s, background-color 0.3s, transform 0.3s;
}
.icon-btn:hover {
  color: #fff;
  background-color: rgb(255 255 255 / 0.1);
}
.icon-btn:active {
  transform: scale(0.9);
}

/* Blinking caret after the typed placeholder text */
.caret {
  display: inline-block;
  width: 1px;
  height: 1em;
  margin-left: 2px;
  vertical-align: -0.15em;
  background: rgb(110 231 183);
  animation: caret-blink 1s steps(1) infinite;
}
@keyframes caret-blink {
  50% { opacity: 0; }
}

.nav-enter {
  animation: nav-drop 0.8s cubic-bezier(0.22, 1, 0.36, 1) both;
}
@keyframes nav-drop {
  from { opacity: 0; transform: translateY(-1.5rem); filter: blur(8px); }
  to { opacity: 1; transform: translateY(0); filter: blur(0); }
}
@media (prefers-reduced-motion: reduce) {
  .nav-enter,
  .caret { animation: none; }
}
</style>