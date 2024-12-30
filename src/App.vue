<script setup>
import {computed, onMounted, provide, reactive, ref, watch} from "vue";

import Header from "@/components/Header.vue";
import CardList from "@/components/CardList.vue";
import axios from "axios";
import Drawer from "@/components/Drawer.vue";


const items = ref([])
const cart = ref([])
const isCreatingOrder = ref(false)

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))
const vatPrice = computed(() => {
  return Math.round((totalPrice.value * 5) / 100);
});
const cartButtonDisabled = computed(() =>
  isCreatingOrder.value ? true : !totalPrice.value)

const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

const drawerOpen = ref(false)
const openDrawer = () => {
  drawerOpen.value = true
}
const closeDrawer = () => {
  drawerOpen.value = false
}

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}
const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value
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

const createOrder = async () => {
  try {
    isCreatingOrder.value = true
    const {data} = await axios.post(`https://f3eb664465d7b49d.mokky.dev/orders`, {
      items: cart.value,
      totalPrice: totalPrice.value,
    })
    cart.value = []
    return data
  } catch (err) {
    console.log(err)
  } finally {
    isCreatingOrder.value = false
  }
}

const addToFavourite = async (item) => {
  try {
    if (!item.isFavourite) {
      const obj = {
        parentId: item.id
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
provide('addToFavourite', addToFavourite)

const addToCart = (item) => {
  cart.value.push(item)
  item.isAdded = true
}
const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}
const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
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
watch(filters, fetchItems)
watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})
watch(cart, () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  {deep: true})
provide('cart', {
  cart,
  closeDrawer,
  openDrawer,
  addToCart,
  removeFromCart,
})
</script>


<template>
  <Drawer v-if="drawerOpen"
          :total-price="totalPrice"
          :vat-price="vatPrice"
          @create-order="createOrder"
          :button-disabled="cartButtonDisabled"
          @close-driver="closeDrawer"
  />
  <div class="bg-amber-50 w-4/5 m-auto rounded-xl shadow-xl mt-14">

    <Header :total-price="totalPrice"
            @open-drawer="openDrawer"/>

    <div class="p-10">
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

    </div>


  </div>
</template>
