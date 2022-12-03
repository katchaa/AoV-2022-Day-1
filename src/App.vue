<script setup>
import { ref } from 'vue'
import { debounce } from 'debounce'

const searchTerm = ref('')
const products = ref([])
const isFetching = ref(false)

const findProducts = debounce(async () => {
  if (searchTerm.value.length === 0) return (products.value = [])
  isFetching.value = true

  try {
    const searchResult = await (
      await fetch(`https://dummyjson.com/products/search?limit=10&q=${searchTerm.value}`)
    ).json()
    products.value = searchResult.products
  } catch (error) {
    alert('Something went wrong, try again!')
  } finally {
    isFetching.value = false
  }
}, 300)
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input
      type="text"
      class="p-2 border-2 border-gray-dark"
      v-model="searchTerm"
      placeholder="Start typing..."
      @input="findProducts()"
    />
    <p v-if="isFetching">Loading...</p>
    <ul v-else class="list-disc flex flex-col items-start">
      <li v-for="product in products" :key="product.id">{{ product.title }} - ${{ product.price }}</li>
    </ul>
  </div>
</template>
