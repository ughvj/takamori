<template>
  <div class="fixed-element">
    <img v-if="correct === 'o'" src="/abstracts/o.png" class="correct-icon" />
    <img v-if="correct === 'x'" src="/abstracts/x.png" class="correct-icon" />
    {{ display }}
    <div class="close-button" @click="clicked"></div>
  </div>
</template>

<script setup>
const props = defineProps({
  id: {
    type: Number,
    required: true,
  },
  display: {
    type: String,
    required: true,
  },
  correct: {
    type: String,
    required: true,
  },
});

const emit = defineEmits();

const clicked = () => {
  emit("deleteElement", props.id);
};
</script>

<style scoped>
.fixed-element {
  display: flex;
  justify-content: center;
}

.correct-icon {
  width: 25px;
  height: 25px;
}

.close-button {
  width: 25px;
  height: 25px;
  cursor: pointer;
}

.close-button::before,
.close-button::after {
  content: "";
  position: absolute;
  width: 10px;
  height: 2px;
  background-color: black;
  border-radius: 2px;
  transform-origin: center;
  transform: translate(-50%, -50%) rotate(45deg);
}

.close-button::after {
  transform: translate(-50%, -50%) rotate(-45deg);
}
</style>
