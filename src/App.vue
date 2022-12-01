<script setup>
import { ref, watch } from 'vue'
import { debounce } from 'debounce'
import axios from 'axios'

const SEARCH_URL = 'https://dummyjson.com/products/search?q='

const searchTerm = ref('')
const products = ref([])
const isLoading = ref(false)

const findProducts = debounce(async (term) => {
  isLoading.value = true
  try {
    if (term.length === 0) return (products.value = [])
  
    const apiResponse = await axios.get(`${SEARCH_URL}${term}`)
    products.value = apiResponse.data.products
  } catch (error) {
    products.value = []
    alert(error)
  } finally {
    isLoading.value = false
  }
}, 300)

watch(searchTerm, newTerm => findProducts(newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input
      type="text"
      class="p-2 border-2 border-gray-dark"
      v-model="searchTerm"
      placeholder="Start typing..."
    />
    <ul class="list-disc" v-if="(!isLoading && products.length > 0)">
      <li v-for="product in products" :key="product.id">
        {{ product.title }}
      </li>
    </ul>
    <div v-if="(isLoading && searchTerm.length > 0)">
      Searching ...
    </div>
  </div>
</template>
