<script setup>
import { ref, watch, provide, computed } from "vue";

import Header from "./components/Header.vue";
import Drawer from "./components/Drawer.vue";

const cartItems = ref([]);
const isDrawerOpen = ref(false);

const totalPrice = computed(() =>
  cartItems.value.reduce((acc, item) => acc + item.price, 0),
);

const vatPrice = computed(() => Math.round(totalPrice.value * 0.05));

const openDrawer = () => {
  isDrawerOpen.value = true;
};

const closeDrawer = () => {
  isDrawerOpen.value = false;
};

const removeFromCart = (item) => {
  cartItems.value.splice(cartItems.value.indexOf(item), 1);
  item.isAdded = false;
};

provide("cart", {
  cartItems,
  openDrawer,
  closeDrawer,
  removeFromCart,
});

watch(
  cartItems,
  () => {
    localStorage.setItem("cartItems", JSON.stringify(cartItems.value));
  },
  { deep: true },
);
</script>

<template>
  <Drawer v-if="isDrawerOpen" :total-price="totalPrice" :vat-price="vatPrice" />
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Header @open-drawer="openDrawer" :total-price="totalPrice" />
    <div class="p-10">
      <RouterView />
    </div>
  </div>
</template>

<style scoped></style>
