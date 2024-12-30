<script setup>
import Card from "@/components/Card.vue";

defineProps({
  items: Array
})
const emit = defineEmits(['addToFavourite', 'addToCart'])

</script>



<template>
  <div class="grid grid-cols-4 gap-5 " v-auto-animate>
    <Card
      v-for="item in items"
      v-if="items.length > 0"
      :v-key="item.id"
      :id="item.id"
      :title="item.title"
      :imageUrl="item.imageUrl"
      :price="item.price"
      :is-favourite="item.isFavourite"
      :onClickFavourite="() => emit('addToFavourite', item)"
      :on-click-add="() => emit('addToCart', item)"
      :is-added="item.isAdded"
    />
    <div v-else >
      <h1>Не нашлось ничего </h1>
    </div>
  </div>
</template>
