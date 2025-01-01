<template>
  <select v-model="selectedOption" @change="changed">
    <option v-if="props.placeholder != ''" value="" disabled selected>
      {{ placeholder }}
    </option>
    <option
      v-for="option in props.options"
      :key="option.value"
      :value="option.value"
    >
      {{ option.label }}
    </option>
  </select>
</template>

<script setup>
const props = defineProps({
  options: {
    type: Array,
    required: true,
  },
  placeholder: {
    type: String,
    default: "",
  },
});

const emit = defineEmits();
const selectedOption = ref("");

const changed = () => {
  emit("update", selectedOption.value);
};
</script>

<style scoped></style>
