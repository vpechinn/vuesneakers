<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";
import CardList from "../components/CardList.vue";

const items = ref([]);

const fetchFavorites = async () => {
  try {
    const { data } = await axios.get(
      "https://75f11136da19f67f.mokky.dev/favorites",
    );

    items.value = data;
  } catch (err) {
    console.log(err);
  }
};

onMounted(async () => {
  await fetchFavorites();
});
</script>

<template>
  <h2 class="text-3xl font-bold">Мои закладки</h2>
  <div class="mt-10" v-auto-animate>
    <CardList :items="items" is-favorites />
  </div>
</template>
