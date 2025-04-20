<template>
  <div class="flex flex-col flex-1 gap-4">
    <CartItem
      v-for="item in items"
      :key="item.id"
      :name="item.title"
      :price="item.price"
      :imageUrl="item.imageUrl"
      @onClickDelete="$emit('deleteCartItem', item)"
    />
  </div>
</template>

<script setup>
import CartItem from "@/components/CartItem.vue";

defineProps({
  items: Array,
});
</script>

<style scoped></style>
np