<template>
  <div class="post-question-area">
    <div class="general-input-division">
      <p>
        <input
          type="text"
          v-model="statement"
          placeholder="問題文を入力"
          size="80"
          required
        />
      </p>

      <SelectBox
        placeholder="カテゴリを選択"
        @update="updateSelectedCategoryOption"
        :options="categoryOptions"
      />
    </div>

    <div class="create-option-division">
      <SelectBox
        placeholder="元勲を選択"
        @update="updateSelectedGenkunOption"
        :options="genkunOptions"
      />
      <SelectBox
        placeholder="正解を選択"
        @update="updateSelectedCorrectOption"
        :options="correctOptions"
      />
      <button @click="appendSelectedInfos">追加</button>
      <button @click="register">登録</button>
    </div>

    <div class="fixed-elements-division">
      <p v-for="info in selectedInfos" :key="info.seq">
        <FixedElement
          @deleteElement="reconstructSelectedInfos"
          :id="info.seq"
          :display="info.genkunNameKanji"
          :correct="info.correct"
        />
      </p>
    </div>
    <InformationMessage :text="message" />
  </div>
</template>

<script setup>
const categoryOptions = ref([]);
const genkunOptions = ref([]);
const correctOptions = ref([]);

const statement = ref("");
const selectedCategoryOption = ref("");
const selectedGenkunOption = ref(0);
const selectedCorrectOption = ref("");

const selectedInfos = ref([]);
const selectedSequence = ref(1);

const message = ref("");

const updateSelectedCategoryOption = (selected) => {
  selectedCategoryOption.value = selected;
};

const updateSelectedGenkunOption = (selected) => {
  selectedGenkunOption.value = selected;
};

const updateSelectedCorrectOption = (selected) => {
  selectedCorrectOption.value = selected;
};

const appendSelectedInfos = () => {
  if (selectedGenkunOption.value == 0) return;
  if (selectedCorrectOption.value == "") return;

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

const register = async () => {
  if (selectedInfos.value.length != 4) {
    message.value = "答えは必ず4つ指定する";
    return;
  }
  await $fetch("http://localhost:2434/question", {
    method: "POST",
    body: {
      statement: statement.value,
      category: selectedCategoryOption.value,
      options: selectedInfos.value.map((e) => {
        return {
          genkun_id: e.genkunId,
          correct: convertCorrectValue(e.correct),
        };
      }),
    },
    headers: {
      "Content-Type": "application/json",
    },
  }).then((res) => {
    message.value = res.message;
  });
};

const convertCorrectValue = (correct) => {
  switch (correct) {
    case "o":
      return true;
    case "x":
      return false;
    case "1":
      return 1;
    case "2":
      return 2;
    case "3":
      return 3;
    case "4":
      return 4;
  }
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

  categoryOptions.value = [
    { value: "choice", label: "4択問題" },
    { value: "order", label: "順番問題" },
  ];

  correctOptions.value = [
    { value: "o", label: "○" },
    { value: "x", label: "×" },
  ];
});
</script>

<style scoped>
.post-question-area {
  display: flex;
  justify-content: center;
  flex-direction: column;
  gap: 30px;
}

.general-input-division {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 20px;
}

.create-option-division {
  display: flex;
  justify-content: center;
  gap: 20px;
}

.fixed-elements-division {
  display: flex;
  justify-content: center;
  gap: 20px;
}
</style>
