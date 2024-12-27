<script setup>
defineProps({
  id: Number,
  title: String,
  imageUrl: String,
  price: Number
})
const emit = defineEmits(['onClickRemove'])
</script>

<template>

  <div class="flex items-center border  border-slate-200 p-4 rounded-xl gap-4 mb-2">
    <img
      :src='imageUrl'
      :alt="title"
      class="w-16 h-16 mr-2">
    <div class="flex flex-col justify-between flex-1">
      <p>{{ title }}</p>
      <div class="flex justify-between w-48 mt-2 items-center">
        <b class="flex-1">{{ price }}</b>
        <img
          @click="emit('onClickRemove')"
          src="/close.svg"
          alt="close"
          class="cursor-pointer opacity-50 hover:opacity-100">
      </div>
    </div>
  </div>

</template>
