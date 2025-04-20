<template>
  <div
    class="relative bg-white border border-slate-10 rounded-3xl p-8 cursor-pointer hover:-translate-y-2 hover:shadow-xl transition"
  >
    <img
      @click="$emit('onClickFavorite')"
      :src="!isFavorite ? '/like-1.svg' : '/like-2.svg'"
      alt="like 2"
      class="absolute top-8 left-8"
    />
    <img :src="imageUrl" alt="Sneakers" />
    <p class="mt-2">{{ title }}</p>

    <div class="flex justify-between mt-5">
      <div class="flex flex-col">
        <span class="text-slate-400">Price</span>
        <b>${{ price }}</b>
      </div>
      <img
        @click="$emit('onClickAdd')"
        :src="isAdded ? '/checked.svg' : '/plus.svg'"
        alt="Plus"
      />
    </div>
  </div>
</template>

<script setup>
defineProps({
  id: Number,
  imageUrl: String,
  title: String,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean,
});
</script>

<style scoped></style>
