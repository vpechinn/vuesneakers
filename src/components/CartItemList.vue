<script setup>
import CartItem from "./CartItem.vue";
import { inject } from "vue";
const { cartItems, removeFromCart } = inject("cart");
</script>

<template>
  <div class="flex flex-col gap-4" v-auto-animate>
    <CartItem
      v-for="item in cartItems"
      :key="item.id"
      :title="item.title"
      :imageUrl="item.imageUrl"
      :price="item.price"
      @on-click-remove="() => removeFromCart(item)"
    />
  </div>
</template>
