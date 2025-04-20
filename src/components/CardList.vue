<template>
  <div class="grid grid-cols-4 gap-5">
    <Card
      v-for="item in items"
      :key="item.id"
      :id="item.id"
      :price="item.price"
      :title="item.title"
      :image-url="item.imageUrl"
      :isFavorite="item.isFavorite"
      :isAdded="item.isAdded"
      @onClickFavorite="$emit('addToFavorite', item)"
      @onClickAdd="$emit('addToCart', item)"
    />
  </div>
</template>

<script setup>
import Card from "@/components/Card.vue";

defineProps({
  items: Array,
});

</script>

<style scoped></style>
