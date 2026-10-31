<script setup>
import { ref, watch, nextTick, onMounted, onUnmounted } from "vue";
import { RouterLink } from "vue-router";
import {
  Clapperboard,
  Instagram,
  Send,
  Disc,
  Search,
  ArrowUp,
  X,
  FileText,
  ShieldCheck,
  Info,
} from "lucide-vue-next";

/* ---------- Scroll reveal (runs once when the footer enters the screen) ---------- */
const footerRef = ref(null);
const visible = ref(false);
let observer = null;

// Stagger helper: each block waits a little longer than the one before it.
const delay = (step) => ({ "--d": `${step * 90}ms` });

/* ---------- Modal ---------- */
const showModal = ref(false);
const activeModal = ref("");
const closeButton = ref(null);

const modalContent = {
  terms: {
    title: "Terms & Conditions",
    icon: FileText,
    description:
      "Welcome to Cineverse Legend. By using our movie browsing and ticket booking services, you agree to the following terms.",
    details: [
      "Please provide accurate information when creating an account or booking tickets.",
      "Review your movie, cinema, showtime, and seat selection before confirming a booking.",
      "Ticket cancellations and refunds are subject to the applicable booking policy.",
      "Cineverse Legend may update these terms when necessary.",
    ],
  },

  privacy: {
    title: "Privacy Policy",
    icon: ShieldCheck,
    description:
      "Cineverse Legend respects your privacy and aims to protect your personal information.",
    details: [
      "Your account information may be used to provide movie ticket booking services.",
      "Keep your account credentials secure and do not share your password.",
      "Personal information should only be accessed by authorized parties.",
      "Contact the Cineverse Legend team if you have questions about your information.",
    ],
  },

  disclosures: {
    title: "Disclosures",
    icon: Info,
    description:
      "Please review the following information about using the Cineverse Legend platform.",
    details: [
      "Movie descriptions, posters, and ratings may come from third-party sources.",
      "Movie schedules, seat availability, and ticket prices may change.",
      "External websites are governed by their own terms and privacy policies.",
      "Always verify your booking details before completing your purchase.",
    ],
  },
};

function openModal(type) {
  activeModal.value = type;
  showModal.value = true;
}

function closeModal() {
  showModal.value = false;
}

function handleKeydown(event) {
  if (event.key === "Escape") closeModal();
}

function scrollToTop() {
  window.scrollTo({ top: 0, behavior: "smooth" });
}

// Lock page scroll behind the modal and move focus into it.
watch(showModal, async (open) => {
  document.body.style.overflow = open ? "hidden" : "";
  if (open) {
    await nextTick();
    closeButton.value?.focus();
  }
});

onMounted(() => {
  window.addEventListener("keydown", handleKeydown);

  if (!("IntersectionObserver" in window)) {
    visible.value = true;
    return;
  }
  observer = new IntersectionObserver(
    ([entry]) => {
      if (entry.isIntersecting) {
        visible.value = true;
        observer.disconnect();
      }
    },
    { threshold: 0.15 },
  );
  observer.observe(footerRef.value);
});

onUnmounted(() => {
  window.removeEventListener("keydown", handleKeydown);
  observer?.disconnect();
  document.body.style.overflow = "";
});
</script>

<template>
  <footer
    ref="footerRef"
    class="footer relative isolate overflow-hidden border-t border-white/10 bg-gradient-to-br from-[#020817]/95 via-[#061525]/80 to-[#03120f]/95 text-white backdrop-blur-xl"
    :class="{ 'is-visible': visible }"
  >
    <!-- Top light line draws in from the center -->
    <div
      class="footer-line absolute inset-x-0 top-0 h-px bg-gradient-to-r from-transparent via-emerald-300/60 to-transparent"
    ></div>

    <!-- Decorative glow (same colors as Login.vue) -->
    <div
      class="glow pointer-events-none absolute -left-32 top-0 -z-10 h-72 w-72 rounded-full bg-emerald-500/20 blur-[120px]"
    ></div>
    <div
      class="glow pointer-events-none absolute -bottom-24 -right-20 -z-10 h-80 w-80 rounded-full bg-cyan-500/15 blur-[120px]"
      style="--d: 300ms"
    ></div>

    <!-- Branding -->
    <div class="border-b border-white/10 px-5 py-12 text-center">
      <RouterLink
        to="/"
        class="reveal group inline-flex items-center gap-3"
        :style="delay(0)"
        aria-label="Cineverse Legend home"
      >
        <span
          class="rounded-2xl border border-emerald-300/25 bg-emerald-400/10 p-3 text-emerald-300 transition duration-500 group-hover:rotate-6 group-hover:border-emerald-300/50 group-hover:bg-emerald-400/20 group-hover:shadow-lg group-hover:shadow-emerald-500/20"
        >
          <Clapperboard :size="25" />
        </span>

        <span class="text-left leading-none">
          <span class="block text-lg font-black tracking-[0.18em]">
            CINEVERSE
          </span>
          <span
            class="mt-1.5 block text-[10px] font-bold tracking-[0.4em] text-emerald-300"
          >
            LEGEND
          </span>
        </span>
      </RouterLink>

      <ul
        class="mt-6 flex flex-wrap items-center justify-center gap-x-8 gap-y-3 text-[10px] tracking-wide text-slate-300 sm:text-xs"
      >
        <li
          v-for="(item, i) in [
            'MOVIES & ENTERTAINMENT',
            'ONLINE TICKET BOOKING',
            'CINEMA EXPERIENCES',
          ]"
          :key="item"
          class="reveal flex items-center gap-2"
          :style="delay(i + 1)"
        >
          <span class="h-1.5 w-1.5 rounded-full bg-emerald-300"></span>
          {{ item }}
        </li>
      </ul>
    </div>

    <!-- Navigation -->
    <div class="px-5 py-10 text-center">
      <nav
        class="flex flex-wrap justify-center gap-x-10 gap-y-5 text-sm text-slate-200"
      >
        <RouterLink
          v-for="(link, i) in [
            { to: '/', label: 'Home' },
            { to: '/movies', label: 'Movies' },
            { to: '/cinemas', label: 'Cinemas' },
            { to: '/bookings', label: 'My Bookings' },
            { to: '/contact', label: 'Contact Us' },
          ]"
          :key="link.to"
          :to="link.to"
          class="reveal group relative py-1 transition-colors duration-300 hover:text-emerald-300"
          :style="delay(i + 4)"
        >
          {{ link.label }}
          <!-- underline grows from the center -->
          <span
            class="absolute inset-x-0 -bottom-0.5 h-px origin-center scale-x-0 bg-emerald-300 transition-transform duration-300 ease-out group-hover:scale-x-100"
          ></span>
        </RouterLink>
      </nav>

      <!-- Social icons -->
      <div class="mt-8 flex justify-center gap-4">
        <a
          v-for="(social, i) in [
            { href: 'https://www.instagram.com/', label: 'Instagram', icon: Instagram, size: 21 },
            { href: 'https://telegram.org/', label: 'Telegram', icon: Send, size: 20 },
            { href: 'https://www.youtube.com/', label: 'YouTube', icon: Disc, size: 21 },
          ]"
          :key="social.label"
          :href="social.href"
          target="_blank"
          rel="noopener noreferrer"
          :aria-label="social.label"
          class="reveal social"
          :style="delay(i + 9)"
        >
          <component :is="social.icon" :size="social.size" />
        </a>

        <RouterLink
          to="/movies"
          aria-label="Explore movies"
          class="reveal social"
          :style="delay(12)"
        >
          <Search :size="21" />
        </RouterLink>
      </div>

      <!-- Legal links: open modal instead of navigating -->
      <div
        class="reveal mt-12 flex flex-wrap items-center justify-center gap-x-4 gap-y-3 text-xs text-slate-200 sm:text-sm"
        :style="delay(13)"
      >
        <button
          type="button"
          class="transition-colors duration-300 hover:text-emerald-300"
          @click="openModal('terms')"
        >
          Terms & Conditions
        </button>

        <span class="text-slate-600">|</span>

        <button
          type="button"
          class="transition-colors duration-300 hover:text-emerald-300"
          @click="openModal('privacy')"
        >
          Privacy Policy
        </button>

        <span class="text-slate-600">|</span>

        <button
          type="button"
          class="transition-colors duration-300 hover:text-emerald-300"
          @click="openModal('disclosures')"
        >
          Disclosures
        </button>
      </div>

      <!-- Copyright + back to top -->
      <div
        class="reveal mt-5 flex items-center justify-center gap-4"
        :style="delay(14)"
      >
        <p class="text-xs text-slate-400 sm:text-sm">
          © {{ new Date().getFullYear() }} Cineverse Legend. All Rights
          Reserved.
        </p>

        <button
          type="button"
          aria-label="Back to top"
          class="group grid h-8 w-8 place-items-center rounded-full border border-white/10 bg-white/5 text-slate-300 transition duration-300 hover:-translate-y-1 hover:border-emerald-300/50 hover:bg-emerald-400/10 hover:text-emerald-300 active:scale-90"
          @click="scrollToTop"
        >
          <ArrowUp
            :size="16"
            class="transition-transform duration-300 group-hover:-translate-y-0.5"
          />
        </button>
      </div>
    </div>
  </footer>

  <!-- Modal popup -->
  <Teleport to="body">
    <Transition name="modal">
      <div
        v-if="showModal"
        class="fixed inset-0 z-[9999] flex items-center justify-center overflow-y-auto bg-[#020817]/70 p-4 backdrop-blur-md"
        @click.self="closeModal"
      >
        <!-- Glass modal card (same style as the Login card) -->
        <div
          class="modal-card relative my-auto w-full max-w-lg overflow-hidden rounded-3xl border border-white/15 bg-slate-950/60 shadow-2xl shadow-black/40 backdrop-blur-2xl"
          role="dialog"
          aria-modal="true"
          :aria-label="modalContent[activeModal]?.title"
        >
          <div
            class="pointer-events-none absolute -right-16 -top-16 h-40 w-40 rounded-full bg-emerald-400/15 blur-3xl"
          ></div>
          <div
            class="pointer-events-none absolute -bottom-20 -left-16 h-40 w-40 rounded-full bg-cyan-500/10 blur-3xl"
          ></div>

          <!-- Modal header -->
          <div
            class="relative flex items-center justify-between gap-4 border-b border-white/10 px-6 py-5"
          >
            <div class="flex items-center gap-3">
              <div
                class="flex h-11 w-11 shrink-0 items-center justify-center rounded-xl border border-emerald-300/25 bg-emerald-400/10 text-emerald-300"
              >
                <component
                  :is="modalContent[activeModal]?.icon"
                  :size="23"
                />
              </div>

              <h2 class="text-lg font-bold text-white sm:text-xl">
                {{ modalContent[activeModal]?.title }}
              </h2>
            </div>

            <button
              ref="closeButton"
              type="button"
              aria-label="Close modal"
              class="shrink-0 rounded-lg p-2 text-slate-400 outline-none transition duration-300 hover:rotate-90 hover:bg-white/10 hover:text-white focus-visible:ring-2 focus-visible:ring-emerald-400/60"
              @click="closeModal"
            >
              <X :size="21" />
            </button>
          </div>

          <!-- Modal content -->
          <div class="relative px-6 py-6">
            <p class="detail text-sm leading-7 text-slate-300" style="--i: 0">
              {{ modalContent[activeModal]?.description }}
            </p>

            <ul class="mt-5 space-y-4">
              <li
                v-for="(detail, index) in modalContent[activeModal]?.details"
                :key="index"
                class="detail flex items-start gap-3 text-sm leading-6 text-slate-400"
                :style="{ '--i': index + 1 }"
              >
                <span
                  class="mt-2 h-1.5 w-1.5 shrink-0 rounded-full bg-emerald-300"
                ></span>

                <span>{{ detail }}</span>
              </li>
            </ul>
          </div>

          <!-- Modal footer -->
          <div
            class="relative flex justify-end border-t border-white/10 px-6 py-4"
          >
            <button
              type="button"
              class="rounded-xl bg-gradient-to-r from-emerald-300 to-teal-300 px-6 py-2.5 text-sm font-bold text-slate-950 shadow-lg shadow-emerald-500/10 transition duration-300 hover:-translate-y-0.5 hover:shadow-xl hover:shadow-emerald-500/20 active:translate-y-0 active:scale-95"
              @click="closeModal"
            >
              Got it
            </button>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<style scoped>
/* ---------- Footer reveal: one staggered entrance when scrolled into view ---------- */
/* Uses animation (not transition) so hover transitions stay free on the same elements. */
.reveal {
  opacity: 0;
}

.is-visible .reveal {
  opacity: 1;
  animation: reveal-up 700ms cubic-bezier(0.2, 0.8, 0.2, 1) var(--d, 0ms)
    backwards;
}

@keyframes reveal-up {
  from {
    opacity: 0;
    transform: translateY(18px);
    filter: blur(6px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
    filter: blur(0);
  }
}

.footer-line {
  transform: scaleX(0);
  transition: transform 1400ms cubic-bezier(0.2, 0.8, 0.2, 1);
}
.is-visible .footer-line {
  transform: scaleX(1);
}

.glow {
  opacity: 0;
  transition: opacity 1600ms ease var(--d, 0ms);
}
.is-visible .glow {
  opacity: 1;
}

/* Social buttons */
.social {
  display: flex;
  height: 2.5rem;
  width: 2.5rem;
  align-items: center;
  justify-content: center;
  border-radius: 0.75rem;
  border: 1px solid rgb(255 255 255 / 0.1);
  background: rgb(255 255 255 / 0.05);
  color: rgb(226 232 240);
  transition:
    transform 300ms ease,
    background-color 300ms ease,
    border-color 300ms ease,
    color 300ms ease,
    box-shadow 300ms ease;
}
.social:hover {
  transform: translateY(-4px);
  border-color: rgb(110 231 183 / 0.5);
  background: rgb(52 211 153 / 0.12);
  color: rgb(110 231 183);
  box-shadow: 0 10px 25px -8px rgb(16 185 129 / 0.35);
}
.social:active {
  transform: scale(0.92);
}

/* ---------- Modal ---------- */
.modal-enter-active,
.modal-leave-active {
  transition: opacity 300ms ease;
}

.modal-enter-active .modal-card {
  transition:
    transform 500ms cubic-bezier(0.2, 0.8, 0.2, 1),
    opacity 400ms ease,
    filter 500ms ease;
}
.modal-leave-active .modal-card {
  transition:
    transform 250ms ease,
    opacity 250ms ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

.modal-enter-from .modal-card {
  opacity: 0;
  transform: translateY(25px) scale(0.97);
  filter: blur(8px);
}
.modal-leave-to .modal-card {
  opacity: 0;
  transform: translateY(12px) scale(0.97);
}

/* Modal text lines fade in one after another */
.modal-enter-active .detail {
  transition:
    opacity 500ms ease calc(var(--i) * 70ms + 150ms),
    transform 500ms ease calc(var(--i) * 70ms + 150ms);
}
.modal-enter-from .detail {
  opacity: 0;
  transform: translateX(-12px);
}

@media (prefers-reduced-motion: reduce) {
  .reveal,
  .footer-line,
  .glow {
    opacity: 1;
    transform: none;
    transition: none;
    animation: none !important;
  }

  .modal-enter-active,
  .modal-leave-active,
  .modal-enter-active .modal-card,
  .modal-leave-active .modal-card,
  .modal-enter-active .detail {
    transition-duration: 0.01ms !important;
    transition-delay: 0ms !important;
  }

  .modal-enter-from .modal-card,
  .modal-enter-from .detail {
    transform: none;
    filter: none;
  }
}
</style>