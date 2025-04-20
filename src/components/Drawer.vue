<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
    <DrawerHead @backArrow="$emit('backToPrevious')" />
    <CartItemList :items="cart" @deleteCartItem="deleteCartItem" />

    <div class="flex flex-col gap-4 mt-7">
      <div class="flex gap-2">
        <span>Total:</span>
        <div class="flex-1 border-b border-dashed"></div>
        <b>${{ total }}</b>
      </div>
      <div class="flex gap-2">
        <span>Tax:</span>
        <div class="flex-1 border-b border-dashed"></div>
        <b>${{ tax }}</b>
      </div>
      <button
        class="mt-4 transition bg-lime-500 w-full rounded-xl py-3 text-white disabled:bg-slate-300 hover:bg-lime-600 active:bg-lime-700 cursor-pointer"
        :disabled="isDisabled"
        v-text="isCreatingOrder ? 'Submitting' : 'Checkout'"
        @click="$emit('createOrder')"
      ></button>
    </div>
  </div>
</template>

<script setup>
import DrawerHead from "@/components/DrawerHead.vue";
import CartItemList from "@/components/CartItemList.vue";
import { computed } from "vue";

const props = defineProps({
  cart: Array,
  total: Number,
  isCreatingOrder: Boolean,
});

const emit = defineEmits();

const deleteCartItem = (item) => {
  emit("deleteCartItem", item);
};

const tax = computed(() => {
  return Number((props.total * 0.15).toFixed(2));
});

const isDisabled = computed(() => {
  return props.cart.length === 0;
});
</script>

<style scoped></style>
