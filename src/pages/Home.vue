<script setup>
import axios from "axios";
import CardList from "../components/CardList.vue";
import debounce from "lodash.debounce";
import { inject, onMounted, reactive, ref, watch } from "vue";
const { cartItems, removeFromCart } = inject("cart");

const items = ref([]);
const filter = reactive({
  sortBy: "title",
  searchValue: "",
});

const onChangeSelect = (event) => {
  filter.sortBy = event.target.value;
};

const onChangeSearchInput = debounce((event) => {
  filter.searchValue = event.target.value;
}, 300);

const addToCart = (item) => {
  cartItems.value.push(item);
  item.isAdded = true;
};

const onClickCartPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item);
  } else {
    removeFromCart(item);
  }
};

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id,
        ...item,
      };

      item.isFavorite = true;

      const { data } = await axios.post(
        `https://75f11136da19f67f.mokky.dev/favorites`,
        obj,
      );

      item.favoriteId = data.id;
    } else {
      item.isFavorite = false;
      await axios.delete(
        `https://75f11136da19f67f.mokky.dev/favorites/${item.favoriteId}`,
      );
      item.favoriteId = null;
    }
  } catch (err) {
    console.log(err);
  }
};

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get(
      "https://75f11136da19f67f.mokky.dev/favorites",
    );
    items.value = items.value.map((item) => {
      const favorite = favorites.find(
        (favorite) => favorite.parentId === item.id,
      );

      if (!favorite) return item;

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

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filter.sortBy,
    };

    if (filter.searchValue) {
      params.title = `*${filter.searchValue}*`;
    }

    const { data } = await axios.get(
      "https://75f11136da19f67f.mokky.dev/items",
      {
        params,
      },
    );
    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      isAdded: false,
      favoriteId: null,
    }));
  } catch (err) {
    console.log(err);
  }
};

onMounted(async () => {
  const localCartItems = localStorage.getItem("cartItems");
  cartItems.value = localCartItems ? JSON.parse(localCartItems) : [];

  await fetchItems();
  await fetchFavorites();

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cartItems.value.some((cartItem) => cartItem.id === item.id),
  }));
});
watch(filter, fetchItems);

watch(cartItems, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false,
  }));
});
</script>

<template>
  <div class="flex justify-between items-center">
    <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

    <div class="flex gap-4">
      <select
        @change="onChangeSelect"
        class="py-2 px-3 border rounded-md outline-none"
      >
        <option value="name">По названию</option>
        <option value="price">По цене(дешевые)</option>
        <option value="-price">По цене(дорогие)</option>
      </select>

      <div class="relative">
        <img src="/search.svg" alt="Search" class="absolute left-4 top-3" />
        <input
          @input="onChangeSearchInput"
          class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
          placeholder="Поиск..."
        />
      </div>
    </div>
  </div>
  <CardList
    :items="items"
    @add-to-favorite="addToFavorite"
    @add-to-cart="onClickCartPlus"
  />
</template>
