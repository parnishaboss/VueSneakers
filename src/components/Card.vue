<script setup>
defineProps({
  imageUrl: String,
  title: String,
  price: Number,
  isFavourite: Boolean,
  isAdded: Boolean,
  onClickAdd: Function,
  onClickFavourite: Function,
})


</script>

<template>
  <div>
    <div
      class="bg-white relative border border-slate-200 rounded-2xl p-8 cursor-pointer transition hover:-translate-y-2 hover:shadow-2xl">
      <img :src="!isFavourite ? '/like-1.svg' : '/like-2.svg'  "
           alt="like-2"
           class="absolute top-8 left-8"
           @click="onClickFavourite"
      >
      <img :src="imageUrl" alt="sneaker">
      <p class="mt-2">{{ title }}</p>

      <div class="flex justify-between mt-5">
        <div class="flex flex-col">
          <span class="text-slate-400">Цена:</span>
          <b>{{ price }}</b>
        </div>
        <img @click="onClickAdd" :src=" !isAdded ? '/plus.svg' : '/checked.svg' " alt="plus">
      </div>
    </div>
  </div>
</template>

