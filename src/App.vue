<template>
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Drawer
        v-if="drawerOpen"
        :cart="cart"
        :total="cartAmount"
        :isCreatingOrder="isCreatingOrder"
        @backToPrevious="cartView"
        @deleteCartItem="deleteCartItem"
        @createOrder="createOrder"
    />
    <Header
        :cartAmount="cartAmount"
        @cartView="cartView"
        @bookmarks="bookmarks"/>
    <div class="p-10">
      <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-8">All items</h2>
        <div class="flex gap-4">
          <select
              @change="onChangeSelect"
              class="py-2 px-3 border rounded-md outline-none"
          >
            <option value="name">Name</option>
            <option value="price">Price (cheaper)</option>
            <option value="-price">Price (expensive)</option>
          </select>
          <div class="relative">
            <img class="absolute left-3 top-3" src="/search.svg"/>
            <input
                @input="onChangeSearchInput"
                class="border rounded-md py-2 pl-10 pr-4 outline-none focus:border-gray-400"
                placeholder="Search..."
            />
          </div>
        </div>
      </div>
      <div class="mt-10">
        <CardList
            :items="items"
            @addToFavorite="addToFavorite"
            @addToCart="addToCart"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
import {onMounted, ref, watch, provide, computed} from "vue";
import axios from "axios";

import Header from "@/components/Header.vue";
import CardList from "@/components/CardList.vue";
import Drawer from "@/components/Drawer.vue";

const items = ref([]);
const cart = ref([]);
const isCreatingOrder = ref(false);

const drawerOpen = ref(false);

const filters = ref({
  sortBy: "title",
  searchQuery: "",
});

const onChangeSelect = (event) => {
  filters.value.sortBy = event.target.value;
};

const onChangeSearchInput = (event) => {
  if (event.target.value.length > 2) {
    filters.value.searchQuery = event.target.value;
  } else computed(filters.value.searchQuery = "")
};

const cartView = () => {
  drawerOpen.value = !drawerOpen.value;
};

const bookmarks = () => {

}

const addToCart = (item) => {
  console.log(item);
  if (item.isAdded) {
    deleteCartItem(item);
    item.isAdded = false;
    return 1;
  }
  item.isAdded = true;
  cart.value.push(item);
};

const createOrder = async () => {
  console.log("createOrder");
  isCreatingOrder.value = true;
  try {
    const {data} = await axios.post(
        `https://0f067a26fc356eb3.mokky.dev/orders`,
        {
          items: cart.value,
          totalPrice: cartAmount.value,
        },
    );
    // for (const item of cart.value) {
    //  item.isAdded = false;
    // }
    cart.value = [];
    return data;
  } catch (error) {
    console.log(error);
  } finally {
    isCreatingOrder.value = false;
  }
};

const deleteCartItem = (itemToDelete) => {
  itemToDelete.isAdded = false;
  cart.value = cart.value.filter((item) => item.id !== itemToDelete.id);
};

const fetchFavorites = async () => {
  try {
    const {data: favorites} = await axios.get(
        `https://0f067a26fc356eb3.mokky.dev/favorites`,
    );
    items.value = items.value.map((item) => {
      const favorite = favorites.find(
          (favorite) => favorite.parentId === item.id,
      );
      if (!favorite) {
        return item;
      }
      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id,
      };
    });
  } catch (err) {
    console.log(err);
  }
};

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id,
      };
      item.isFavorite = true;
      const {data} = await axios.post(
          `https://0f067a26fc356eb3.mokky.dev/favorites`,
          obj,
      );
      item.favoriteId = data.id;
      console.log(data);
    } else {
      item.isFavorite = false;
      await axios.delete(
          `https://0f067a26fc356eb3.mokky.dev/favorites/${item.favoriteId}`,
      );
      item.favoriteId = null;
      console.log(item.id, item.favoriteId);
    }
  } catch (err) {
    console.log(err);
  }
};
const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.value.sortBy,
    };

    if (filters.value.searchQuery) {
      params.title = `*${filters.value.searchQuery}*`;
    }

    const {data} = await axios.get(
        `https://0f067a26fc356eb3.mokky.dev/items`,
        {
          params,
        },
    );
    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false,
    }));
  } catch (err) {
    console.log(err);
  }
  await fetchFavorites();
};

onMounted(async () => {
  await fetchItems();
  // await fetchFavorites();
});

const cartAmount = computed(() => {
  let total = 0;
  for (let i = 0; i < cart.value.length; i++) {
    total += cart.value[i].price;
  }
  return total;
});

watch(filters.value, fetchItems);

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false,
  }));
});
</script>
