<script setup lang="ts">
import {defineProps} from "vue";

const props = defineProps<{
  card: {
    id: number;
    title: string;
    description: string;
    category: string;
    price: number;
    discountPercentage: number;
    rating: number;
    brand: string;
    images: string[];
    thumbnail: string;
    stock: number;
  };
}>();

const {
  title,
  description,
  category,
  price,
  discountPercentage,
  rating,
  brand,
  thumbnail,

} = props.card;

</script>

<template>
  <div class="card">
    <img :src="thumbnail" :alt="title"/>
    <div class="content">
      <h2>{{ title }}</h2>
      <p class="description">{{ description }}</p>
      <div class="discount-brand-info">
        <span class="discountPercentage">Discount: {{ discountPercentage }}%</span>
        <span class="brand">Brand: {{ brand }}</span>
      </div>
      <div class="product-pricing-rating">
        <span class="price">$ {{ price }}</span>
        <span class="price">Rating: {{ rating }}</span>
        <span class="category">{{ category }}</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
.content {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.discount-brand-info {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 30px;
}

.discountPercentage {
  font-size: 14px;
  font-weight: 600;
  color: red;
}

.brand {
  font-size: 14px;
  font-weight: 600;
  color: #007bff;
}

.product-pricing-rating {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 30px;
}

.price {
  font-size: 14px;
  font-weight: 600;
  color: cornflowerblue;
}

.category {
  padding: 5px 10px;
  border-radius: 20px;
  background: #09cd09;
  color: #284228;
}

</style>
