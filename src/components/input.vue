<script setup lang="ts">
import { defineProps, defineEmits, defineModel } from "vue";
const model = defineModel();

defineProps<{
  modelValue: string | number;
  type?: "text" | "number";
}>();

const handleChange = (event: any) => {
  model.value = event.target.value;
  console.log(model.value);
  
};
</script>

<template>
  <input :type="type" :value="modelValue" @input="handleChange"v-model="model" />
</template>

<style lang="">
/* Add styles if needed */
</style>
