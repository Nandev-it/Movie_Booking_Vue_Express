
<script setup>
import { ref } from 'vue'
import {
  Clapperboard,
  Mail,
  LockKeyhole,
  Eye,
  EyeOff,
  ArrowRight,
  Sparkles,
  ShieldCheck
} from 'lucide-vue-next'

const email = ref('')
const password = ref('')
const showPassword = ref(false)
const rememberMe = ref(true)

const handleLogin = () => {
  // Static UI only — no API request
  alert('Static login UI demo')
}
</script>

<template>
  <main class="relative isolate flex min-h-screen items-center justify-center
               overflow-hidden bg-[#06111e] px-4 py-8 text-white sm:px-8">

    <!-- Cambodian festival background -->
    <div class="absolute inset-0 -z-30 bg-[url('/images/cambodian-festival.png')]
                bg-cover bg-center animate-background"></div>

    <!-- Dark overlay -->
    <div class="absolute inset-0 -z-20
                bg-gradient-to-br from-[#020817]/95
                via-[#061525]/80 to-[#03120f]/95"></div>

    <!-- Decorative glow -->
    <div class="absolute -left-32 top-1/4 -z-10 h-80 w-80
                rounded-full bg-emerald-500/20 blur-[120px]"></div>

    <div class="absolute -bottom-32 -right-20 -z-10 h-96 w-96
                rounded-full bg-cyan-500/15 blur-[120px]"></div>

    <div class="w-full max-w-5xl">

      <!-- Navbar -->
      <header class="mb-8 flex items-center justify-between animate-down">
        <a href="/" class="flex items-center gap-3">
          <div class="rounded-2xl border border-emerald-300/25
                      bg-emerald-400/10 p-3 text-emerald-300">
            <Clapperboard :size="25" />
          </div>

          <div>
            <h1 class="text-lg font-black tracking-[0.18em]">
              CINEVERSE
            </h1>
            <p class="text-[10px] font-bold tracking-[0.4em] text-emerald-300">
              LEGEND
            </p>
          </div>
        </a>

        <a href="/"
           class="rounded-xl border border-white/15 bg-white/5
                  px-4 py-2.5 text-sm text-slate-200 backdrop-blur-xl
                  transition duration-300 hover:border-emerald-300/50
                  hover:bg-emerald-400/10">
          Home
        </a>
      </header>

      <section class="grid items-center gap-10 md:grid-cols-2 md:gap-16">

        <!-- Left introduction -->
        <div class="hidden animate-left md:block">
          <div class="mb-6 inline-flex items-center gap-2 rounded-full
                      border border-emerald-300/20 bg-emerald-300/10
                      px-4 py-2 text-[10px] font-bold tracking-[0.18em]
                      text-emerald-200">
            <Sparkles :size="15" />
            YOUR NEXT STORY STARTS HERE
          </div>

          <h2 class="text-5xl font-black leading-tight tracking-tight lg:text-6xl">
            Enter the
            <span class="block bg-gradient-to-r from-emerald-200
                         via-emerald-400 to-cyan-300 bg-clip-text
                         text-transparent">
              world of movies.
            </span>
          </h2>

          <p class="mt-6 max-w-md text-sm leading-7 text-slate-300">
            Discover unforgettable stories, explore the latest releases,
            and reserve your favorite cinema seats with Cineverse Legend.
          </p>

          <div class="mt-9 flex items-center gap-4">
            <div class="rounded-xl border border-white/10
                        bg-white/5 p-3 text-emerald-300">
              <Clapperboard :size="22" />
            </div>
            <div>
              <p class="font-semibold">Your cinema, your choice.</p>
              <p class="mt-1 text-xs text-slate-400">
                Discover. Select. Book. Enjoy.
              </p>
            </div>
          </div>
        </div>

        <!-- Static login card -->
        <div class="login-card relative w-full rounded-3xl
                    border border-white/15 bg-slate-950/60
                    p-6 shadow-2xl shadow-black/40 backdrop-blur-2xl
                    sm:p-9">

          <div class="pointer-events-none absolute -right-12 -top-12
                      h-36 w-36 rounded-full bg-emerald-400/10 blur-3xl"></div>

          <div class="relative">
            <div class="mb-6 inline-flex rounded-2xl border
                        border-emerald-300/20 bg-emerald-400/10
                        p-3 text-emerald-300">
              <Clapperboard :size="25" />
            </div>

            <p class="text-xs font-bold tracking-[0.25em] text-emerald-300">
              WELCOME BACK
            </p>

            <h2 class="mt-3 text-2xl font-bold sm:text-3xl">
              Sign in to Cineverse
            </h2>

            <p class="mt-2 text-sm text-slate-400">
              Continue your cinematic journey.
            </p>

            <form @submit.prevent="handleLogin" class="mt-8 space-y-5">

              <!-- Email -->
              <div>
                <label for="email"
                       class="mb-2 block text-sm font-medium text-slate-200">
                  Email address
                </label>

                <div class="flex items-center gap-3 rounded-xl border
                            border-white/10 bg-white/[0.04] px-4
                            transition duration-300 focus-within:border-emerald-400/70
                            focus-within:ring-4 focus-within:ring-emerald-400/10">
                  <Mail :size="19" class="shrink-0 text-slate-400" />

                  <input id="email" v-model="email" type="email"
                         placeholder="you@example.com"
                         class="min-w-0 flex-1 bg-transparent py-4 text-sm
                                text-white outline-none placeholder:text-slate-500" />
                </div>
              </div>

              <!-- Password -->
              <div>
                <div class="mb-2 flex items-center justify-between">
                  <label for="password"
                         class="text-sm font-medium text-slate-200">
                    Password
                  </label>

                  <a href="/forgot-password"
                     class="text-xs text-emerald-300 transition hover:text-emerald-200">
                    Forgot password?
                  </a>
                </div>

                <div class="flex items-center gap-3 rounded-xl border
                            border-white/10 bg-white/[0.04] px-4
                            transition duration-300 focus-within:border-emerald-400/70
                            focus-within:ring-4 focus-within:ring-emerald-400/10">
                  <LockKeyhole :size="19" class="shrink-0 text-slate-400" />

                  <input id="password" v-model="password"
                         :type="showPassword ? 'text' : 'password'"
                         placeholder="Enter your password"
                         class="min-w-0 flex-1 bg-transparent py-4 text-sm
                                text-white outline-none placeholder:text-slate-500" />

                  <button type="button"
                          @click="showPassword = !showPassword"
                          class="text-slate-400 transition hover:text-emerald-300"
                          :aria-label="showPassword ? 'Hide password' : 'Show password'">
                    <EyeOff v-if="showPassword" :size="19" />
                    <Eye v-else :size="19" />
                  </button>
                </div>
              </div>

              <!-- Remember me -->
              <label class="flex cursor-pointer items-center gap-2.5
                            text-sm text-slate-400">
                <input v-model="rememberMe" type="checkbox"
                       class="h-4 w-4 accent-emerald-400" />
                Remember me
              </label>

              <!-- Button -->
              <button type="submit"
                      class="group flex w-full items-center justify-center gap-3
                             rounded-xl bg-gradient-to-r from-emerald-300
                             to-teal-300 px-5 py-4 text-sm font-bold
                             text-slate-950 shadow-lg shadow-emerald-500/10
                             transition duration-300 hover:-translate-y-0.5
                             hover:shadow-xl hover:shadow-emerald-500/20
                             active:translate-y-0">
                Sign In
                <ArrowRight :size="19"
                            class="transition duration-300 group-hover:translate-x-1" />
              </button>
            </form>

            <div class="my-7 flex items-center gap-3">
              <div class="h-px flex-1 bg-white/10"></div>
              <span class="text-[9px] font-bold tracking-[0.2em] text-slate-500">
                MOVIE LOVERS
              </span>
              <div class="h-px flex-1 bg-white/10"></div>
            </div>

            <p class="text-center text-sm text-slate-400">
              Don't have an account?
              <a href="/register"
                 class="ml-1 font-semibold text-emerald-300 transition
                        hover:text-emerald-200">
                Create account
              </a>
            </p>

            <div class="mt-6 flex items-center justify-center gap-2
                        border-t border-white/10 pt-5 text-xs text-slate-500">
              <ShieldCheck :size="16" class="text-emerald-300" />
              Secure access to your bookings
            </div>
          </div>
        </div>
      </section>

      <footer class="mt-10 flex flex-wrap justify-between gap-3
                     text-[10px] tracking-wider text-slate-500">
        <span>© 2026 Cineverse Legend</span>
        <span>MADE FOR MOVIE LOVERS</span>
      </footer>
    </div>
  </main>
</template>

<style scoped>
.login-card {
  animation: card-enter 800ms cubic-bezier(0.2, 0.8, 0.2, 1) both;
}

.animate-down {
  animation: slide-down 700ms ease both;
}

.animate-left {
  animation: slide-left 850ms 100ms ease both;
}

.animate-background {
  animation: background-enter 1200ms ease both;
}

@keyframes card-enter {
  from {
    opacity: 0;
    transform: translateY(25px) scale(0.97);
    filter: blur(8px);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
    filter: blur(0);
  }
}

@keyframes slide-down {
  from {
    opacity: 0;
    transform: translateY(-15px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes slide-left {
  from {
    opacity: 0;
    transform: translateX(-25px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

@keyframes background-enter {
  from {
    opacity: 0;
    transform: scale(1.15);
  }
  to {
    opacity: 1;
    transform: scale(1.1);
  }
}

@media (prefers-reduced-motion: reduce) {
  .login-card,
  .animate-down,
  .animate-left,
  .animate-background {
    animation: none;
  }

  * {
    transition-duration: 0.01ms !important;
  }
}
</style>
