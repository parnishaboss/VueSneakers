<script setup>
import DrawerHead from "@/components/DrawerHead.vue";
import CartItemList from "@/components/CartItemList.vue";
import InfoBlock from "@/components/InfoBlock.vue";
import axios from "axios";
import {computed, inject, ref} from "vue";

const props = defineProps({
  totalPrice: Number,
  vatPrice: Number,
  buttonDisabled: Boolean
})

const isCreatingOrder = ref(false)
const orderId = ref(null)
const {cart, closeDrawer} = inject('cart')

const buttonDisabled = computed(() =>
  isCreatingOrder.value ? true : !props.totalPrice)

const createOrder = async () => {
  try {
    isCreatingOrder.value = true
    const {data} = await axios.post(`https://f3eb664465d7b49d.mokky.dev/orders`, {
      items: cart.value,
      totalPrice: props.totalPrice,
    })
    cart.value = []

    orderId.value = data.id
    return data
  } catch (err) {
    console.log(err)
  } finally {
    isCreatingOrder.value = false
  }
}

</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black/70 z-10">
    <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-9 flex flex-col">
      <DrawerHead/>
      <div v-if="totalPrice === 0 || orderId" class="flex flex-col h-full justify-center">
          <InfoBlock
            v-if="!totalPrice && !orderId"
            description=""
            title="Корзина пустая"
            image-url="/package-icon.png"
          />
          <InfoBlock
            v-if="orderId"
            :description= "`Ваш заказ #${orderId} успешно оформлен.`"
            title="Заказ оформлен!"
            image-url="/order-success-icon.png"
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
                @click="createOrder"
                class="mt-4 cursor-pointer transition bg-lime-500 w-full rounded-xl py-3 text-white hover:bg-lime-600 active:bg-lime-700 disabled:bg-slate-400">
          Оформить заказ
        </button>
      </div>
    </div>
  </div>
</template>

