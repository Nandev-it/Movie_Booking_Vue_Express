
<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import { RouterLink } from "vue-router";
import {
  Instagram,
  Send,
  Disc,
  Search,
  X,
  FileText,
  ShieldCheck,
  Info,
} from "lucide-vue-next";

const showModal = ref(false);
const activeModal = ref("");

// Modal content
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
  if (event.key === "Escape") {
    closeModal();
  }
}

onMounted(() => {
  window.addEventListener("keydown", handleKeydown);
});

onUnmounted(() => {
  window.removeEventListener("keydown", handleKeydown);
});
</script>

<template>
  <footer class="border-t border-white/15 bg-[#0e1114] text-white">
    <!-- Branding -->
    <div class="border-b border-white/20 px-5 py-12 text-center">
      <RouterLink to="/" class="inline-flex items-center gap-2">
        <span class="text-3xl font-black">◧</span>

        <span class="text-xl font-medium tracking-wide sm:text-2xl">
          CINEVERSE
          <span class="font-black text-violet-400">LEGEND</span>
        </span>
      </RouterLink>

      <div
        class="mt-5 flex flex-wrap items-center justify-center gap-x-8 gap-y-3 text-[10px] tracking-wide text-gray-300 sm:text-xs"
      >
        <span>● MOVIES & ENTERTAINMENT</span>
        <span>● ONLINE TICKET BOOKING</span>
        <span>● CINEMA EXPERIENCES</span>
      </div>
    </div>

    <!-- Navigation -->
    <div class="px-5 py-10 text-center">
      <nav
        class="flex flex-wrap justify-center gap-x-10 gap-y-5 text-sm text-gray-200"
      >
        <RouterLink to="/" class="transition hover:text-violet-400">
          Home
        </RouterLink>

        <RouterLink to="/movies" class="transition hover:text-violet-400">
          Movies
        </RouterLink>

        <RouterLink to="/cinemas" class="transition hover:text-violet-400">
          Cinemas
        </RouterLink>

        <RouterLink to="/bookings" class="transition hover:text-violet-400">
          My Bookings
        </RouterLink>

        <RouterLink to="/contact" class="transition hover:text-violet-400">
          Contact Us
        </RouterLink>
      </nav>

      <!-- Social icons -->
      <div class="mt-8 flex justify-center gap-5">
        <a
          href="https://www.instagram.com/"
          target="_blank"
          rel="noopener noreferrer"
          aria-label="Instagram"
          class="flex h-10 w-10 items-center justify-center rounded-xl bg-[#24272b] transition hover:-translate-y-1 hover:bg-violet-600"
        >
          <Instagram :size="21" />
        </a>

        <a
          href="https://telegram.org/"
          target="_blank"
          rel="noopener noreferrer"
          aria-label="Telegram"
          class="flex h-10 w-10 items-center justify-center rounded-xl bg-[#24272b] transition hover:-translate-y-1 hover:bg-violet-600"
        >
          <Send :size="20" />
        </a>

        <a
          href="https://www.youtube.com/"
          target="_blank"
          rel="noopener noreferrer"
          aria-label="YouTube"
          class="flex h-10 w-10 items-center justify-center rounded-xl bg-[#24272b] transition hover:-translate-y-1 hover:bg-violet-600"
        >
          <Disc :size="21" />
        </a>

        <RouterLink
          to="/movies"
          aria-label="Explore movies"
          class="flex h-10 w-10 items-center justify-center rounded-xl bg-[#24272b] transition hover:-translate-y-1 hover:bg-violet-600"
        >
          <Search :size="22" />
        </RouterLink>
      </div>

      <!-- Legal links: open modal instead of navigating -->
      <div
        class="mt-12 flex flex-wrap items-center justify-center gap-x-4 gap-y-3 text-xs text-gray-200 sm:text-sm"
      >
        <button
          type="button"
          @click="openModal('terms')"
          class="transition hover:text-violet-400"
        >
          Terms & Conditions
        </button>

        <span class="text-gray-500">|</span>

        <button
          type="button"
          @click="openModal('privacy')"
          class="transition hover:text-violet-400"
        >
          Privacy Policy
        </button>

        <span class="text-gray-500">|</span>

        <button
          type="button"
          @click="openModal('disclosures')"
          class="transition hover:text-violet-400"
        >
          Disclosures
        </button>
      </div>

      <!-- Copyright -->
      <p class="mt-4 text-xs text-gray-400 sm:text-sm">
        © {{ new Date().getFullYear() }} Cineverse Legend. All Rights Reserved.
      </p>
    </div>
  </footer>

  <!-- Modal popup -->
  <Teleport to="body">
    <Transition name="modal">
      <div
        v-if="showModal"
        class="fixed inset-0 z-[9999] flex items-center justify-center overflow-y-auto bg-black/60 p-4 backdrop-blur-md"
        @click.self="closeModal"
      >
        <!-- Glass modal card -->
        <div
          class="modal-card relative my-auto w-full max-w-lg overflow-hidden rounded-2xl border border-white/10 bg-[#11131d]/90 shadow-2xl shadow-violet-950/40 backdrop-blur-2xl"
          role="dialog"
          aria-modal="true"
          :aria-label="modalContent[activeModal]?.title"
        >
          <!-- Violet glow -->
          <div
            class="pointer-events-none absolute -right-16 -top-16 h-40 w-40 rounded-full bg-violet-600/20 blur-3xl"
          ></div>

          <div
            class="pointer-events-none absolute -bottom-20 -left-16 h-40 w-40 rounded-full bg-indigo-600/10 blur-3xl"
          ></div>

          <!-- Modal header -->
          <div
            class="relative flex items-center justify-between gap-4 border-b border-white/10 px-6 py-5"
          >
            <div class="flex items-center gap-3">
              <div
                class="flex h-11 w-11 shrink-0 items-center justify-center rounded-xl border border-violet-400/20 bg-violet-600/15 text-violet-400"
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
              type="button"
              @click="closeModal"
              aria-label="Close modal"
              class="shrink-0 rounded-lg p-2 text-gray-400 transition hover:bg-white/10 hover:text-white"
            >
              <X :size="21" />
            </button>
          </div>

          <!-- Modal content -->
          <div class="relative px-6 py-6">
            <p class="text-sm leading-7 text-gray-300">
              {{ modalContent[activeModal]?.description }}
            </p>

            <ul class="mt-5 space-y-4">
              <li
                v-for="(detail, index) in modalContent[activeModal]?.details"
                :key="index"
                class="flex items-start gap-3 text-sm leading-6 text-gray-400"
              >
                <span
                  class="mt-2 h-1.5 w-1.5 shrink-0 rounded-full bg-violet-400"
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
              @click="closeModal"
              class="rounded-xl bg-violet-600 px-6 py-2.5 text-sm font-semibold text-white transition hover:bg-violet-500 active:scale-95"
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
.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.25s ease;
}

.modal-enter-active .modal-card,
.modal-leave-active .modal-card {
  transition:
    transform 0.25s ease,
    opacity 0.25s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

.modal-enter-from .modal-card,
.modal-leave-to .modal-card {
  opacity: 0;
  transform: translateY(16px) scale(0.96);
}
</style>
