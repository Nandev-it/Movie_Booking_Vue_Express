
<script setup>
import { Film } from "lucide-vue-next";
import { RouterLink } from "vue-router";
</script>

<template>
  <header
    class="sticky top-0 z-40 border-b border-white/10 bg-[#080b12]/85 backdrop-blur-xl text-white"
  >
    <nav
      class="mx-auto flex max-w-[1600px] items-center justify-between px-5 py-4 sm:px-8 lg:px-12"
    >
      <!-- Logo -->
      <RouterLink to="/" class="flex items-center gap-3">
        <div
          class="flex h-10 w-10 items-center justify-center rounded-xl bg-violet-600 shadow-lg shadow-violet-600/30"
        >
          <Film :size="22" />
        </div>

        <div>
          <h1 class="text-lg font-black tracking-wide sm:text-xl">
            CINEVERSE <span class="text-violet-400">LEGEND</span>
          </h1>
          <p class="text-[10px] tracking-[0.25em] text-gray-500">
            YOUR MOVIE UNIVERSE
          </p>
        </div>
      </RouterLink>

      <!-- Navigation links -->
      <div class="hidden items-center gap-8 text-sm text-gray-400 md:flex">
        <RouterLink to="/" class="transition hover:text-white">
          Home
        </RouterLink>

        <a href="#horror" class="transition hover:text-white">Horror</a>
        <a href="#animation" class="transition hover:text-white">Animation</a>
        <a href="#action" class="transition hover:text-white">Action</a>
      </div>

      <!-- Sign in -->
      <RouterLink
        to="/login"
        class="rounded-full bg-white/10 px-5 py-2.5 text-sm font-semibold transition hover:bg-violet-600"
      >
        Sign In
      </RouterLink>
    </nav>
  </header>
</template>
