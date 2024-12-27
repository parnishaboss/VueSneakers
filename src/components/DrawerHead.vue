<script setup>
  import {inject} from "vue";

  const {closeDrawer} = inject('cart')

</script>

<template>
  <div class="flex items-center gap-5 mb-5">
    <img @click="closeDrawer" src="/arrow-right.svg " class="rotate-180 cursor-pointer hover:-translate-x-1 transition hover:scale-125 p-2">
    <h2 class="text-2xl font-bold">Корзина</h2>
  </div>
</template>
