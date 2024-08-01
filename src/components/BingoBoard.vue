<template>
  <h1>Bingo Board</h1>
  <h1>{{ randomNumber }}</h1>
  <div class="wrapper" :class="{ complete: completed }">
    <div
      class="box"
      v-for="number in boardNumbers"
      :key="`key-${number}`"
      :class="{ checked: number.checked }"
    >
      {{ number.value }}
    </div>
  </div>
  <div class="btn-wrapper">
    <button class="btn" :disabled="disableStart" @click="start">Start</button>
    <button class="btn" @click="reset">Refresh</button>
  </div>
  <div v-show="completed">
    <h1>You are winner!</h1>
  </div>
</template>
<script setup lang="ts">
import { onMounted, ref, watch } from "vue";

interface BoardNumber {
  value: number;
  checked: boolean;
}

const completed = ref<boolean>(false);
const boardNumbers = ref<Array<BoardNumber>>([]);
const startInterval = ref();
const disableStart = ref<boolean>(false);
const count = ref<number>(0);
const randomNumber = ref<number | null>(null);

const generateRandomNumber = () => {
  const min = 1;
  const max = 99;
  return Math.floor(Math.random() * (max - min + 1)) + min;
};

const reset = () => {
  disableStart.value = false;
  completed.value = false;
  count.value = 0;
  boardNumbers.value = [];
  clearInterval(startInterval.value);
  randomNumber.value = null;
  initializeBoard();
};
const initializeBoard = () => {
  while (boardNumbers.value.length < 25) {
    const random = generateRandomNumber();
    const mapedNumbers = boardNumbers.value.map((el) => el.value);
    if (!mapedNumbers.includes(random)) {
      boardNumbers.value.push({ value: random, checked: false });
    }
  }
};

const checkBoardNumbers = () => {
  randomNumber.value = generateRandomNumber();
  const numberIndex = boardNumbers.value.findIndex(
    (el) => el.value === randomNumber.value && !el.checked
  );
  if (numberIndex !== -1) {
    console.log("numberIndex: ", numberIndex);
    boardNumbers.value[numberIndex].checked = true;
    count.value++;
    console.log("count.value: ", count.value);
  }
};
const start = () => {
  disableStart.value = true;
  startInterval.value = setInterval(checkBoardNumbers, 100);
};
watch(count, (value) => {
  if (value >= 25) {
    clearInterval(startInterval.value);
    completed.value = true;
  }
});
onMounted(() => {
  initializeBoard();
});
</script>
