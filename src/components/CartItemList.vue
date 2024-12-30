<script setup>
import CartItem from "@/components/CartItem.vue";
import {inject} from "vue";

const {cart, removeFromCart} = inject('cart')

</script>

<template>
  <div class="flex flex-col gap-3 " v-auto-animate>
    <CartItem v-for="item in cart"
              :v-key="item.id"
              :title="item.title"
              :image-url="item.imageUrl"
              :price="item.price"
              @on-click-remove="() => removeFromCart(item)"
    />
  </div>

</template>

