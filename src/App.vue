<script setup>
import {onMounted, ref} from "vue";

import Header from "@/components/Header.vue";
import CardList from "@/components/CardList.vue";
import axios from "axios";


  const items = ref([])
  onMounted( async () => {
    try {
      const {data} = await axios.get('https://f3eb664465d7b49d.mokky.dev/items')
      items.value = data
    } catch (err) {
      console.error('Ошибка при запросе', err)
    }
  })

</script>


<template>
  <!--  <Drawer/>-->
  <div class="bg-amber-50 w-4/5 m-auto rounded-xl shadow-xl mt-14">

    <Header/>

    <div class="p-10">
      <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>
        <div class="flex gap-4">
          <select class="py-2 px-3 border border-gray-300 rounded-md outline-none">
            <option>По названию</option>
            <option>По цене (сразу дешёвые)</option>
            <option>По цене (сразу дорогие)</option>
          </select>

          <div class="relative">
            <img class="absolute left-3 top-3" src="/search.svg" alt="">
            <input class="border border-gray-300 rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-500"
                   placeholder="Поиск...">
          </div>
        </div>

      </div>
      <div class="mt-10">
        <CardList :items="items"/>
      </div>

    </div>


  </div>
</template>
