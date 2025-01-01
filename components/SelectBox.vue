<template>
  <div>
    <select v-model="selectedOption" @change="changed">
      <option
        v-for="option in props.options"
        :key="option.value"
        :value="option.value"
      >
        {{ option.label }}
      </option>
    </select>

    <!-- 現在の選択 -->
    <p>選択された値: {{ selectedOption }}</p>
  </div>
</template>

<script setup>
const props = defineProps({
  options: {
    type: Array,
    required: true,
  },
});

const emit = defineEmits();
const selectedOption = ref();

const changed = () => {
  emit("update", selectedOption.value);
};

watch(
  () => props.options,
  () => {
    selectedOption.value = props.options[0].value;
  }
);
</script>

<style scoped></style>
