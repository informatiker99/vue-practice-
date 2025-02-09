<template>
  <div class="flex items-center ">
    <div class="flex flex-col">
      <label for="" v-if="props.label">{{ props.label }}</label>
      <input :type="props.type" :value="displayValue" @input="handleInput" @blur="handleBlur"
        @beforeinput="validateInput" />
    </div>
    <span v-if="props.iconName">{{ iconName }}</span>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue';

const props = defineProps({
  modelValue: {
    type: String,
    default: ''
  },
  label: {
    type: String,
    default: ''
  },
  iconName: {
    type: String,
    default: ''
  },
});

const emit = defineEmits(['update:modelValue']);

const rawValue = ref(props.modelValue);

// Format display value with commas
const displayValue = computed(() => {
  const [integer, decimal] = rawValue.value.split('.');
  const formattedInteger = integer.replace(/\B(?=(\d{3})+(?!\d))/g, ',');
  return decimal !== undefined ? `${formattedInteger}.${decimal}` : formattedInteger;
});

watch(() => props.modelValue, (newVal) => {
  if (newVal !== rawValue.value) {
    rawValue.value = newVal;
  }
});

function validateInput(event) {
  const newChar = event.data;
  const currentValue = event.target.value;
  const selectionStart = event.target.selectionStart;

  // Allow backspace/delete
  if (event.inputType.includes('delete')) return true;

  // Prevent invalid characters
  if (newChar && !/[0-9.,]/.test(newChar)) {
    event.preventDefault();
    return false;
  }


  // const pattern = /^-?\d*\.\d{2}$/
  // console.log(newChar);


  // Handle dot validation
  if (newChar === '.') {
    if (currentValue.includes('.')) {
      event.preventDefault();
      return false;
    }

    // If dot is first character, allow but will be formatted later
    if (selectionStart === 0) return true;
  }

  // Ensure the inpt value includes just two decimals 
  if (currentValue.includes('.')) {
    const [integerPart, decimalPart] = currentValue.split('.');

    if (decimalPart && decimalPart.length >= 2) {
      event.preventDefault();
      return false;
    }
  }

  if(currentValue.includes(''))

  return true;
}

function handleInput(e) {
  let newValue = e.target.value.replace(/,/g, '');

  // Format leading dot
  if (newValue.startsWith('.')) {
    newValue = '0' + newValue;
  }



  // Ensure only one dot
  const parts = newValue.split('.');
  if (parts.length > 2) {
    newValue = parts[0] + '.' + parts.slice(1).join('');
  }

  // Remove leading zeros except for 0 before dot
  newValue = newValue.replace(/^0+(?=\d)/, '');

  rawValue.value = newValue
  emit('update:modelValue', newValue);

}

function handleBlur() {
  // Add trailing zero if ends with dot
  if (rawValue.value.endsWith('.')) {
    rawValue.value += '0';
    emit('update:modelValue', rawValue.value);
  }

  // Add leading zero if empty after dot
  if (rawValue.value.startsWith('0.') && rawValue.value.length === 2) {
    rawValue.value = '0.0';
    emit('update:modelValue', rawValue.value);
  }
}
</script>