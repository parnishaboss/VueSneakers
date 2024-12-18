<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black/70 z-10">
    <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-9 flex flex-col">
      <DrawerHead/>
      <CartItemList/>

      <div class="flex flex-col gap-4 mt-auto">
        <div class="flex gap-2">
          <span>Итого:</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>12900 руб.</b>
        </div>
        <div class="flex gap-2">
          <span>Налог 5%</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>900 руб.</b>
        </div>
        <button disabled class="mt-4 cursor-pointer transition bg-lime-500 w-full rounded-xl py-3 text-white hover:bg-lime-600 active:bg-lime-700 disabled:bg-slate-400">
          Оформить заказ
        </button>
      </div>
    </div>
  </div>
</template>
<script setup>
import DrawerHead from "@/components/DrawerHead.vue";

import CartItemList from "@/components/CartItemList.vue";
</script>
