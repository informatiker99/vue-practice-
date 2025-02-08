<template>
  <FormattedInput v-model="amount" />
  <p>Raw Value: {{ amount }}</p>
  <p>Formatted Value: {{ formatAsCurrency(amount) }}</p>
</template>

<script setup>
import { ref } from 'vue';
import FormattedInput from './components/FormattedInput.vue';

const amount = ref('');
const formatAsCurrency = (value) => {
  const [integer, decimal] = value.split('.');
  return `${integer.replace(/\B(?=(\d{3})+(?!\d))/g, ',')}${decimal ? `.${decimal}` : ''}`;
};
</script>