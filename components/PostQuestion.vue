<template>
  <SelectBox @update="updateSelectedGenkunOption" :options="genkunOptions" />
  <SelectBox @update="updateSelectedCorrectOption" :options="correctOptions" />
  <button @click="appendSelectedInfos">追加</button>
  <p v-for="info in selectedInfos" :key="info.seq">
    <FixedElement
      @deleteElement="reconstructSelectedInfos"
      :id="info.seq"
      :display="info.genkunNameKanji"
      :correct="info.correct"
    />
  </p>
</template>

<script setup>
const genkunOptions = ref([]);
const correctOptions = ref([]);

const selectedGenkunOption = ref(0);
const selectedCorrectOption = ref(false);

const selectedInfos = ref([]);
const selectedSequence = ref(1);

const updateSelectedGenkunOption = (selected) => {
  selectedGenkunOption.value = selected;
};

const updateSelectedCorrectOption = (selected) => {
  selectedCorrectOption.value = selected;
};

const appendSelectedInfos = () => {
  selectedInfos.value = [
    ...selectedInfos.value,
    {
      seq: selectedSequence.value,
      genkunId: selectedGenkunOption.value,
      genkunNameKanji: genkunOptions.value.find(
        (e) => e.value == selectedGenkunOption.value
      ).label,
      correct: selectedCorrectOption.value,
    },
  ];
  selectedSequence.value++;
};

const reconstructSelectedInfos = (id) => {
  selectedInfos.value = selectedInfos.value.reduce((acc, cur) => {
    if (cur.seq == id) {
      return acc;
    }
    return [...acc, cur];
  }, []);
};

onMounted(async () => {
  await $fetch("http://localhost:2434/genkuns", {}).then((res) => {
    console.log(res);
    genkunOptions.value = res.reduce((acc, cur) => {
      return [
        ...acc,
        {
          value: cur.id,
          label: cur.name_kanji,
        },
      ];
    }, []);
  });

  correctOptions.value = [
    { value: "o", label: "○" },
    { value: "x", label: "×" },
  ];

  selectedGenkunOption.value = genkunOptions.value[0].value;
  selectedCorrectOption.value = "o";
});
</script>

<style scoped></style>
