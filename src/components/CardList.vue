<script setup lang="ts">
import {ref, onMounted, watch} from "vue";
import Card from "./Card.vue";

interface Product {
  id: number;
  title: string;
  description: string;
  category: string;
  price: number;
  discountPercentage: number;
  rating: number;
  stock: number;
  tags: string[];
  brand: string;
  images: string[];
  thumbnail: string;
}

const cards = ref<Product[]>([]);
const searchQuery = ref("");
const limit = ref(20);
const skip = ref(0);
const hasMore = ref(true);
const isLoading = ref(false);

const fetchProducts = async (query: string, reset = false) => {
  if (isLoading.value || !hasMore.value) return;

  try {
    isLoading.value = true;

    const url = query
        ? `https://dummyjson.com/products/search?q=${query}&limit=${limit.value}&skip=${skip.value}`
        : `https://dummyjson.com/products?limit=${limit.value}&skip=${skip.value}`;

    const response = await fetch(url);
    const data = await response.json();

    if (reset) {
      cards.value = data.products;
    } else {
      cards.value = [...cards.value, ...data.products];
    }

    if (data.products.length < limit.value) {
      hasMore.value = false;
    } else {
      skip.value += limit.value;
    }
  } catch (error) {
    console.error("Error fetching data:", error);
  } finally {
    isLoading.value = false;
  }
};


const debounce = (fn: Function, delay: number) => {
  let timeout: number;
  return (...args: any[]) => {
    clearTimeout(timeout);
    timeout = setTimeout(() => fn(...args), delay);
  };
};


const updateSearchQuery = debounce((query: string) => {
  skip.value = 0;
  hasMore.value = true;
  fetchProducts(query, true);
}, 500);


watch(searchQuery, (newQuery) => {
  updateSearchQuery(newQuery);
});


const handleScroll = () => {
  const {scrollTop, scrollHeight, clientHeight} = document.documentElement;
  if (scrollTop + clientHeight >= scrollHeight - 50) {
    fetchProducts(searchQuery.value);
  }
};


onMounted(() => {
  fetchProducts("");
  window.addEventListener("scroll", handleScroll);
});

</script>

<template>
  <input
      class="search-input"
      type="text"
      v-model="searchQuery"
      placeholder="Search for a product"
  />

  <div v-if="!isLoading && cards.length === 0" class="no-products">
    No products found.
  </div>

  <div v-if="cards.length > 0" class="cards">
    <transition-group
        name="fade"
        tag="ul"
        class="card-list"
    >
      <li class="card" v-for="card in cards" :key="card.id">
        <Card :card="card"/>
      </li>
    </transition-group>
  </div>

  <div v-if="isLoading" class="loader">Loading...</div>
</template>


<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.search-input {
  width: 300px;
  padding: 10px;
  background-color: white;
  border-radius: 5px;
}

ul {
  list-style-type: none;
  padding: 5px;
}

.cards {
  background-color: white;
  border-radius: 5px;
}

.card {
  padding: 2em;
  min-width: 500px;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 20px;
}

.loader {
  text-align: center;
  padding: 10px;
  color: gray;
}

.no-products {
  text-align: center;
  color: gray;
  padding: 20px;
  font-size: 1.2em;
}

</style>
