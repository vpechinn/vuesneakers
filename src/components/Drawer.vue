<script setup>
import DrawHead from "./DrawHead.vue";
import CartItemList from "./CartItemList.vue";
import InfoBlock from "./InfoBlock.vue";
import axios from "axios";
import { computed, inject, ref } from "vue";
const props = defineProps({
  totalPrice: Number,
  vatPrice: Number,
});

const isCreatingOrder = ref(false);
const orderId = ref(null);

const { cartItems } = inject("cart");

const cartIsEmpty = computed(() => cartItems.value.length === 0);

const buttonDisabled = computed(
  () => isCreatingOrder.value || cartIsEmpty.value,
);

const createOrder = async () => {
  try {
    isCreatingOrder.value = true;
    const { data } = await axios.post(
      "https://75f11136da19f67f.mokky.dev/orders",
      { item: cartItems.value, totalPrice: props.totalPrice.value },
    );

    cartItems.value = [];
    orderId.value = data.id;
    return data;
  } catch (err) {
    console.log(err);
  } finally {
    isCreatingOrder.value = false;
  }
};
</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8 flex flex-col">
    <DrawHead />
    <CartItemList />

    <InfoBlock
      v-if="orderId"
      title="Заказа оформлен!"
      image-url="/order-success-icon.png"
      :description="`Ваш заказ #${orderId} скоро будет передан курьерской доставке`"
    />

    <InfoBlock
      v-if="!totalPrice && !orderId"
      title="Коризна пустая"
      image-url="/package-icon.png"
      description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ."
    />

    <div v-if="totalPrice" class="flex flex-col gap-4 mt-7">
      <div class="flex gap-2">
        <span>Итого:</span>
        <div class="flex-1 border-b border-dashed"></div>
        <b>{{ totalPrice }} руб.</b>
      </div>

      <div class="flex gap-2">
        <span>Налог 5%</span>
        <div class="flex-1 border-b border-dashed"></div>
        <b>{{ vatPrice }}.</b>
      </div>

      <button
        :disabled="buttonDisabled"
        @click="createOrder"
        class="mt-4 bg-lime-500 w-full rounded-xl py-3 text-white hover:bg-lime-600 active:bg-lime-700 disabled:bg-slate-300 cursor-pointer transition"
      >
        Оформить заказ
      </button>
    </div>
  </div>
</template>
