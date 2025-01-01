<script setup>
import  debounce from 'lodash.debounce'
import CardList from "@/components/CardList.vue";
import {inject, onMounted, reactive, ref, watch} from "vue";
import axios from "axios";


const items = ref([])
const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})
const {cart, addToCart, removeFromCart} = inject('cart')

const addToFavourite = async (item) => {
  try {
    if (!item.isFavourite) {
      const obj = {
        parentId: item.id,
        item
      }
      item.isFavourite = true // чтобы раньше отрисовывался UX до запроса
      const {data} = await axios.post(`https://f3eb664465d7b49d.mokky.dev/favourites`, obj)
      item.favouriteId = data.id
    } else {
      item.isFavourite = false // чтобы раньше отрисовывался UX до запроса
      await axios.delete(`https://f3eb664465d7b49d.mokky.dev/favourites/${item.favouriteId}`,)
      item.favouriteId = null
    }

  } catch (err) {
    console.log(err)
  }

}
const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}
const onChangeSearchInput = debounce( (event) => {
  filters.searchQuery = event.target.value
}, 500 )
const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy,
    }
    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }

    const {data} = await axios.get(
      `https://f3eb664465d7b49d.mokky.dev/items`, {
        params
      }
    )
    items.value = data.map(obj => ({
      ...obj,
      isFavourite: false,
      favouriteId: null,
      isAdded: false
    }))
  } catch (err) {
    console.error('Ошибка при запросе', err)
  }
}
const fetchFavourites = async () => {
  try {
    const {data: favourites} = await axios.get(
      `https://f3eb664465d7b49d.mokky.dev/favourites`)
    items.value = items.value.map(item => {
      const favourite = favourites.find(f => f.parentId === item.id)
      if (!favourite) {
        return item
      }
      return {
        ...item,
        isFavourite: true,
        favouriteId: favourite.id
      }
    })
    // console.log(items.value)
  } catch (err) {
    console.error('Ошибка при запросе', err)
  }
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []

  await fetchItems()
  await fetchFavourites()

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))
})

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})

watch(filters, fetchItems)
</script>

<template>
  <div class="flex justify-between items-center">
    <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>
    <div class="flex gap-4">
      <select @change="onChangeSelect"
              class="py-2 px-3 border border-gray-300 rounded-md outline-none">
        <option value="name">По названию</option>
        <option value="price">По цене (сразу дешёвые)</option>
        <option value="-price">По цене (сразу дорогие)</option>
      </select>

      <div class="relative">
        <img class="absolute left-3 top-3" src="/search.svg" alt="">
        <input
          @input="onChangeSearchInput"
          class="border border-gray-300 rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-500"
          placeholder="Поиск...">
      </div>
    </div>

  </div>
  <div class="mt-10">
    <CardList :items="items" @add-to-favourite="addToFavourite" @add-to-cart="onClickAddPlus"/>
  </div>
</template>

