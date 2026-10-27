<script setup>
import { ref } from 'vue'
import {
  Clapperboard,
  UserRound,
  Mail,
  LockKeyhole,
  Eye,
  EyeOff,
  ArrowRight,
  Sparkles,
  ShieldCheck,
  Check
} from 'lucide-vue-next'

const name = ref('')
const email = ref('')
const password = ref('')
const confirmPassword = ref('')
const showPassword = ref(false)
const showConfirmPassword = ref(false)
const agreeToTerms = ref(false)

const handleRegister = () => {
  if (password.value !== confirmPassword.value) {
    alert('Passwords do not match.')
    return
  }

  if (!agreeToTerms.value) {
    alert('Please agree to the Terms of Service and Privacy Policy.')
    return
  }

  // Static UI only — no API request
  alert('Static registration UI demo')
}
</script>

<template>
  <main
    class="relative isolate flex min-h-screen items-center justify-center overflow-hidden bg-[#06111e] px-4 py-8 text-white sm:px-8"
  >
    <!-- Background image: place your image at public/images/cambodian-festival.png -->
    <div
      class="absolute inset-0 -z-30 bg-[url('/images/cambodian-festival.png')] bg-cover bg-center animate-background"
    ></div>

    <!-- Dark overlay -->
    <div
      class="absolute inset-0 -z-20 bg-gradient-to-br from-[#020817]/95 via-[#061525]/80 to-[#03120f]/95"
    ></div>

    <!-- Decorative glow -->
    <div
      class="absolute -left-32 top-1/4 -z-10 h-80 w-80 rounded-full bg-emerald-500/20 blur-[120px]"
    ></div>
    <div
      class="absolute -bottom-32 -right-20 -z-10 h-96 w-96 rounded-full bg-cyan-500/15 blur-[120px]"
    ></div>

    <div class="w-full max-w-5xl">
      <!-- Navbar -->
      <header class="mb-8 flex items-center justify-between animate-down">
        <RouterLink to="/" class="flex items-center gap-3">
          <div
            class="rounded-2xl border border-emerald-300/25 bg-emerald-400/10 p-3 text-emerald-300"
          >
            <Clapperboard :size="25" />
          </div>

          <div>
            <h1 class="text-lg font-black tracking-[0.18em]">CINEVERSE</h1>
            <p class="text-[10px] font-bold tracking-[0.4em] text-emerald-300">
              LEGEND
            </p>
          </div>
        </RouterLink>

        <RouterLink
          to="/"
          class="rounded-xl border border-white/15 bg-white/5 px-4 py-2.5 text-sm text-slate-200 backdrop-blur-xl transition duration-300 hover:border-emerald-300/50 hover:bg-emerald-400/10"
        >
          Home
        </RouterLink>
      </header>

      <section class="grid items-center gap-10 md:grid-cols-2 md:gap-16">
        <!-- Left introduction -->
        <div class="hidden animate-left md:block">
          <div
            class="mb-6 inline-flex items-center gap-2 rounded-full border border-emerald-300/20 bg-emerald-300/10 px-4 py-2 text-[10px] font-bold tracking-[0.18em] text-emerald-200"
          >
            <Sparkles :size="15" />
            YOUR NEXT STORY STARTS HERE
          </div>

          <h2 class="text-5xl font-black leading-tight tracking-tight lg:text-6xl">
            Join the
            <span
              class="block bg-gradient-to-r from-emerald-200 via-emerald-400 to-cyan-300 bg-clip-text text-transparent"
            >
              movie experience.
            </span>
          </h2>

          <p class="mt-6 max-w-md text-sm leading-7 text-slate-300">
            Create your Cineverse Legend account to discover new releases,
            choose your favorite seats, and keep your bookings in one place.
          </p>

          <div class="mt-9 flex items-center gap-4">
            <div
              class="rounded-xl border border-white/10 bg-white/5 p-3 text-emerald-300"
            >
              <ShieldCheck :size="22" />
            </div>
            <div>
              <p class="font-semibold">Your cinema, your choice.</p>
              <p class="mt-1 text-xs text-slate-400">
                Discover. Select. Book. Enjoy.
              </p>
            </div>
          </div>
        </div>

        <!-- Register card -->
        <div
          class="register-card relative w-full rounded-3xl border border-white/15 bg-slate-950/60 p-6 shadow-2xl shadow-black/40 backdrop-blur-2xl sm:p-9"
        >
          <div
            class="pointer-events-none absolute -right-12 -top-12 h-36 w-36 rounded-full bg-emerald-400/10 blur-3xl"
          ></div>

          <div class="relative">
            <div
              class="mb-6 inline-flex rounded-2xl border border-emerald-300/20 bg-emerald-400/10 p-3 text-emerald-300"
            >
              <Clapperboard :size="25" />
            </div>

            <p class="text-xs font-bold tracking-[0.25em] text-emerald-300">
              START YOUR JOURNEY
            </p>

            <h2 class="mt-3 text-2xl font-bold sm:text-3xl">
              Create your account
            </h2>

            <p class="mt-2 text-sm text-slate-400">
              Sign up to start your cinematic journey.
            </p>

            <form @submit.prevent="handleRegister" class="mt-7 space-y-4">
              <!-- Name -->
              <div>
                <label
                  for="name"
                  class="mb-2 block text-sm font-medium text-slate-200"
                >
                  Full name
                </label>

                <div
                  class="flex items-center gap-3 rounded-xl border border-white/10 bg-white/[0.04] px-4 transition duration-300 focus-within:border-emerald-400/70 focus-within:ring-4 focus-within:ring-emerald-400/10"
                >
                  <UserRound :size="19" class="shrink-0 text-slate-400" />
                  <input
                    id="name"
                    v-model.trim="name"
                    type="text"
                    autocomplete="name"
                    placeholder="Enter your full name"
                    required
                    class="min-w-0 flex-1 bg-transparent py-3.5 text-sm text-white outline-none placeholder:text-slate-500"
                  />
                </div>
              </div>

              <!-- Email -->
              <div>
                <label
                  for="email"
                  class="mb-2 block text-sm font-medium text-slate-200"
                >
                  Email address
                </label>

                <div
                  class="flex items-center gap-3 rounded-xl border border-white/10 bg-white/[0.04] px-4 transition duration-300 focus-within:border-emerald-400/70 focus-within:ring-4 focus-within:ring-emerald-400/10"
                >
                  <Mail :size="19" class="shrink-0 text-slate-400" />
                  <input
                    id="email"
                    v-model.trim="email"
                    type="email"
                    autocomplete="email"
                    placeholder="you@example.com"
                    required
                    class="min-w-0 flex-1 bg-transparent py-3.5 text-sm text-white outline-none placeholder:text-slate-500"
                  />
                </div>
              </div>

              <!-- Password -->
              <div>
                <label
                  for="password"
                  class="mb-2 block text-sm font-medium text-slate-200"
                >
                  Password
                </label>

                <div
                  class="flex items-center gap-3 rounded-xl border border-white/10 bg-white/[0.04] px-4 transition duration-300 focus-within:border-emerald-400/70 focus-within:ring-4 focus-within:ring-emerald-400/10"
                >
                  <LockKeyhole :size="19" class="shrink-0 text-slate-400" />
                  <input
                    id="password"
                    v-model="password"
                    :type="showPassword ? 'text' : 'password'"
                    autocomplete="new-password"
                    placeholder="Create a password"
                    minlength="8"
                    required
                    class="min-w-0 flex-1 bg-transparent py-3.5 text-sm text-white outline-none placeholder:text-slate-500"
                  />
                  <button
                    type="button"
                    @click="showPassword = !showPassword"
                    class="text-slate-400 transition hover:text-emerald-300"
                    :aria-label="showPassword ? 'Hide password' : 'Show password'"
                  >
                    <EyeOff v-if="showPassword" :size="19" />
                    <Eye v-else :size="19" />
                  </button>
                </div>
                <p class="mt-1.5 text-xs text-slate-500">
                  Use at least 8 characters.
                </p>
              </div>

              <!-- Confirm password -->
              <div>
                <label
                  for="confirmPassword"
                  class="mb-2 block text-sm font-medium text-slate-200"
                >
                  Confirm password
                </label>

                <div
                  class="flex items-center gap-3 rounded-xl border border-white/10 bg-white/[0.04] px-4 transition duration-300 focus-within:border-emerald-400/70 focus-within:ring-4 focus-within:ring-emerald-400/10"
                >
                  <LockKeyhole :size="19" class="shrink-0 text-slate-400" />
                  <input
                    id="confirmPassword"
                    v-model="confirmPassword"
                    :type="showConfirmPassword ? 'text' : 'password'"
                    autocomplete="new-password"
                    placeholder="Enter your password again"
                    minlength="8"
                    required
                    class="min-w-0 flex-1 bg-transparent py-3.5 text-sm text-white outline-none placeholder:text-slate-500"
                  />
                  <button
                    type="button"
                    @click="showConfirmPassword = !showConfirmPassword"
                    class="text-slate-400 transition hover:text-emerald-300"
                    :aria-label="showConfirmPassword ? 'Hide password' : 'Show password'"
                  >
                    <EyeOff v-if="showConfirmPassword" :size="19" />
                    <Eye v-else :size="19" />
                  </button>
                </div>
              </div>

              <!-- Terms -->
              <label
                class="flex cursor-pointer items-start gap-2.5 text-xs leading-5 text-slate-400"
              >
                <input
                  v-model="agreeToTerms"
                  type="checkbox"
                  required
                  class="mt-1 h-4 w-4 shrink-0 accent-emerald-400"
                />
                <span>
                  I agree to the
                  <a href="/terms" class="text-emerald-300 hover:text-emerald-200">
                    Terms of Service
                  </a>
                  and
                  <a
                    href="/privacy"
                    class="text-emerald-300 hover:text-emerald-200"
                  >
                    Privacy Policy
                  </a>.
                </span>
              </label>

              <!-- Submit -->
              <button
                type="submit"
                class="group flex w-full items-center justify-center gap-3 rounded-xl bg-gradient-to-r from-emerald-300 to-teal-300 px-5 py-4 text-sm font-bold text-slate-950 shadow-lg shadow-emerald-500/10 transition duration-300 hover:-translate-y-0.5 hover:shadow-xl hover:shadow-emerald-500/20 active:translate-y-0"
              >
                Create Account
                <ArrowRight
                  :size="19"
                  class="transition duration-300 group-hover:translate-x-1"
                />
              </button>
            </form>

            <div class="my-6 flex items-center gap-3">
              <div class="h-px flex-1 bg-white/10"></div>
              <span class="text-[9px] font-bold tracking-[0.2em] text-slate-500">
                MOVIE LOVERS
              </span>
              <div class="h-px flex-1 bg-white/10"></div>
            </div>

            <p class="text-center text-sm text-slate-400">
              Already have an account?
              <RouterLink
                to="/login"
                class="ml-1 font-semibold text-emerald-300 transition hover:text-emerald-200"
              >
                Sign in
              </RouterLink>
            </p>

            <div
              class="mt-6 flex items-center justify-center gap-2 border-t border-white/10 pt-5 text-xs text-slate-500"
            >
              <Check :size="16" class="text-emerald-300" />
              One account for your movie bookings
            </div>
          </div>
        </div>
      </section>

      <footer
        class="mt-10 flex flex-wrap justify-between gap-3 text-[10px] tracking-wider text-slate-500"
      >
        <span>© 2026 Cineverse Legend</span>
        <span>MADE FOR MOVIE LOVERS</span>
      </footer>
    </div>
  </main>
</template>

<style scoped>
.register-card {
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
  .register-card,
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
