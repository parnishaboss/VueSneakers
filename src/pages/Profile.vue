

<script setup>
import {onMounted, ref} from "vue";
import axios from "axios";

  const orders = ref([])
  onMounted(async () => {
    try {
      const {data} = await axios.get(`https://f3eb664465d7b49d.mokky.dev/orders`)
      orders.value = data
      console.log(data)
    } catch (err) {
      console.log(err)
    }
  })
</script>

<template>
  <h2 class="text-3xl font-bold mb-8">История заказов</h2>
  <ul>
    <li class="mb-5" v-for="item in orders" :key="item.id">
      <h2 class="text-2xl font-medium mb-2">Ваш заказ #{{item.id}}</h2>
      <div class="flex flex-col">
        <span class="text-xl mb-2  ">Сумма заказа:
        <span class="text-red-900 font-bold">{{item.totalPrice}} руб.</span>
        </span>
        <span v-for="sneakers in item.items">
          {{sneakers.title}}
        </span>
      </div>
    </li>
  </ul>
</template>
