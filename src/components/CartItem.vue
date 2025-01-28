<script setup>
defineProps({
  title: String,
  imageUrl: String,
  price: Number,
});

const emit = defineEmits(["on-click-remove"]);
</script>

<template>
  <div class="flex items-center border border-slate-100 p-4 rounded-xl gap-4">
    <img class="w-16 h-16" :src="imageUrl" alt="Sneaker" />

    <div class="flex flex-col flex-1">
      <p>{{ title }}</p>
      <div class="flex justify-between">
        <b class="flex-1">{{ price }} руб.</b>
        <img
          @click="emit('on-click-remove')"
          class="opacity-40 hover:opacity-100 cursor-pointer transition"
          src="/close.svg"
          alt="Close"
        />
      </div>
    </div>
  </div>
</template>
