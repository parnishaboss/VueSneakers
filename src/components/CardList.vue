<script setup>
defineProps({
  items: Array
})

import Card from "@/components/Card.vue";
const onClickAdd = () => {
  alert('DOM')
}
</script>



<template>
  <div class="grid grid-cols-4 gap-5 ">
    <Card
      v-for="item in items"
      :v-key="item.id"
      :title="item.title"
      :imageUrl="item.imageUrl"
      :price="item.price"
      :onClickAdd="onClickAdd"
    />
  </div>
</template>
