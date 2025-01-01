<template>
  <div class="information-message">
    <span>{{ displayedText }}</span>
  </div>
</template>

<script setup>
const props = defineProps({
  text: {
    type: String,
    required: true,
  },
});

const displayedText = ref("");

watch(
  () => props.text,
  (newValue) => {
    displayedText.value = newValue;
  }
);

const startTyping = (index) => {
  const type = () => {
    if (index < props.text.length) {
      displayedText.value += props.text[index];
      index++;
      setTimeout(type, props.typingSpeed);
    }
  };

  type();
};
</script>

<style scoped></style>
