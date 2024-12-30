<script setup>
import DrawerHead from "@/components/DrawerHead.vue";
import CartItemList from "@/components/CartItemList.vue";
import InfoBlock from "@/components/InfoBlock.vue";
import {inject} from "vue";

defineProps({
  totalPrice: Number,
  vatPrice: Number,
  buttonDisabled: Boolean
})
const emit = defineEmits(['createOrder'])
const {closeDrawer} = inject('cart')

</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black/70 z-10">
    <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-9 flex flex-col">
      <DrawerHead/>

      <div v-if="totalPrice === 0" class="flex flex-col h-full justify-center">
        <InfoBlock
          description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ."
          title="Корзина пустая"
          image-url="/package-icon.png"
        />
        <button
                @click="closeDrawer"
                class="mt-8 cursor-pointer transition bg-lime-500 w-full rounded-xl py-3 text-white hover:bg-lime-600 active:bg-lime-700 ">
          Вернуться назад
        </button>
      </div>
      <CartItemList v-else/>
      <div v-if="totalPrice" class="flex flex-col gap-4 mt-auto">
        <div class="flex gap-2">
          <span>Итого:</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ totalPrice }} руб.</b>
        </div>
        <div class="flex gap-2">
          <span>Налог 5%</span>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ vatPrice }} руб.</b>
        </div>
        <button :disabled="buttonDisabled"
                @click="() => emit('createOrder')"
                class="mt-4 cursor-pointer transition bg-lime-500 w-full rounded-xl py-3 text-white hover:bg-lime-600 active:bg-lime-700 disabled:bg-slate-400">
          Оформить заказ
        </button>
      </div>
    </div>
  </div>
</template>

