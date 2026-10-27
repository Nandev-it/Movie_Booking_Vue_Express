
<script setup>
import { ref, onMounted } from 'vue'
import api from '../services/api'

// Reactive state
const products = ref([])
const loading = ref(true)
const error = ref(null)

// Fetch products using Axios
const fetchProducts = async () => {
  try {
    error.value = null

    const response = await api.get('/products')

    // Express returns { success: true, data: results }
    products.value = response.data.data
  } catch (err) {
    error.value =
      err.response?.data?.message ||
      err.message ||
      'Failed to fetch products'
  } finally {
    loading.value = false
  }
}

// Fetch data when component loads
onMounted(() => {
  fetchProducts()
})
</script>

<template>
  <div class="min-h-screen bg-gray-100 p-8">
    <div class="mx-auto max-w-4xl">

      <!-- Header -->
      <h1 class="mb-6 text-center text-3xl font-bold text-gray-800">
        Our Products
      </h1>

      <!-- Loading State -->
      <div
        v-if="loading"
        class="py-10 text-center text-gray-500"
      >
        Loading products from database...
      </div>

      <!-- Error State -->
      <div
        v-else-if="error"
        class="mb-4 rounded border border-red-400 bg-red-100 px-4 py-3 text-red-700"
      >
        {{ error }}
        <button
          @click="fetchProducts"
          class="ml-2 font-semibold underline"
        >
          Try Again
        </button>
      </div>

      <!-- Products Grid -->
      <div
        v-else-if="products.length > 0"
        class="grid grid-cols-1 gap-6 md:grid-cols-3"
      >
        <div
          v-for="product in products"
          :key="product.id"
          class="rounded-lg border border-gray-200 bg-white p-5 shadow-md"
        >
          <h2 class="mb-2 text-xl font-semibold text-gray-700">
            {{ product.name }}
          </h2>

          <p class="mb-4 text-sm text-gray-600">
            {{ product.description || 'No description haha' }}
          </p>

          <div class="flex items-center justify-between">
            <span class="text-lg font-bold text-green-600">
              ${{ product.price }}
            </span>

            <button
              class="rounded bg-blue-500 px-3 py-1.5 text-sm text-white transition hover:bg-blue-600"
              @click="alert(`Selected: ${product.name}`)"
            >
              Buy Now
            </button>
          </div>
        </div>
      </div>

      <!-- Empty State -->
      <div
        v-else
        class="py-10 text-center text-gray-500"
      >
        No products found in the database.
      </div>

    </div>
  </div>
</template>
