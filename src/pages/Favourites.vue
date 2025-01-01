<script setup>
import axios from "axios";
import {onMounted, ref} from "vue";
import CardList from "@/components/CardList.vue";

const favourites = ref([])
onMounted(async () => {
  try {
    const {data} = await axios.get(`https://f3eb664465d7b49d.mokky.dev/favourites`)
    favourites.value = data.map(obj => obj.item)
  } catch (err) {
    console.log(err)
  }
})
</script>


<template>
  <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>
  <CardList :items="favourites" is-favourites/>
</template>
