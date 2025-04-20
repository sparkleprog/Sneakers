<template>
  <div class="flex items-center gap-5 mb-5">
    <img
        @click="$emit('backArrow')"
      src="/arrow-next.svg"
      class="opacity-30 bg-black cursor-pointer rotate-180 hover:opacity-100 transition hover:-translate-x-1"
    />
    <h2 class="text-2xl font-bold ho">Basket</h2>
  </div>
</template>

<script setup></script>

<style scoped></style>
