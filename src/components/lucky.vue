<script setup>
import { NButton, NSpace, NFlex } from 'naive-ui';
import { ref, computed } from 'vue';
import { useStorage } from '@vueuse/core';
const inPara = defineProps({
  num: {
    type: Number,
    default: 1,
  },
});
const running = ref(false);
const stopping = ref(false);
const total = useStorage(
  'total',
  Array(70)
    .fill(1)
    .map((v, k) => k + 1),
  sessionStorage
);
const showText = ref(
  Array(inPara.num)
    .fill(1)
    .map((v, k) => '?')
);

const wenhao = computed(() => {
  return showText.value.filter((i) => i == '?').length;
});
const buchou = ref([]);

const drawnNumbers = useStorage('drawnNumbers', [], sessionStorage);
let timer = null;
function start() {
  running.value = true;
  timer = setInterval(() => {
    showText.value = makeNumbers(total.value, inPara.num);
  }, 80);
}

function startBuchou() {
  running.value = true;
  timer = setInterval(() => {
    let res = makeNumbers(total.value, buchou.value.length);
    buchou.value.forEach((i, index) => {
      showText.value[i] = res[index];
    });
  }, 80);
}

function stop() {
  if (buchou.value.length > 0) {
    let res = makeNumbers(total.value, buchou.value.length, false);
    buchou.value.forEach((i, index) => {
      showText.value[i] = res[index];
    });
  } else {
    showText.value = makeNumbers(total.value, inPara.num, false);
  }

  stopping.value = true;
  running.value = false;
  clearInterval(timer);
}

function reset() {
  stopping.value = false;
  running.value = false;
  showText.value = Array(inPara.num)
    .fill(1)
    .map((v, k) => '?');
  drawnNumbers.value = [];
  total.value = Array(70)
    .fill(1)
    .map((v, k) => k + 1);
  buchou.value = 0;

  clearInterval(timer);
}

function makeNumbers(numbers, inNum = 5, isShow = true) {
  // 用于存储已经抽取过的数字

  // 筛选出还未被抽取过的数字
  const availableNumbers = numbers.filter((inNum) => !drawnNumbers.value.includes(inNum));

  // 如果剩余可用数字不足 5 个，给出提示并返回空数组
  if (availableNumbers.length < inNum) {
    console.log('剩余数字不足 5 个，无法继续抽取。');
    return [];
  }

  const result = [];
  // 循环 5 次进行抽取
  for (let i = 0; i < inNum; i++) {
    // 生成一个随机索引，范围是 0 到 availableNumbers 的长度减 1
    const randomIndex = Math.floor(Math.random() * availableNumbers.length);
    // 从可用数字中取出一个数字
    const drawn = availableNumbers[randomIndex];
    // 将该数字添加到结果数组中
    result.push(drawn);
    if (!isShow) {
      // 将该数字添加到已抽取数字数组中
      drawnNumbers.value.push(drawn);
    }

    // 从可用数字数组中移除该数字
    availableNumbers.splice(randomIndex, 1);
  }
  return result;
}

function abort(item, index) {
  showText.value[index] = '?';
  buchou.value.push(index);
}
</script>
<template>
  <n-flex justify="center">
    <div v-for="(item, index) in showText" style="margin: 40px 0; text-align: center">
      <n-flex class="num" align="center" justify="center">
        {{ item }}
      </n-flex>
      <n-button v-if="!running && item != '?'" text type="primary" @click="abort(item, index)">作废</n-button>
    </div>
  </n-flex>

  <n-space justify="center">
    <n-button v-if="!running && wenhao == num" type="primary" @click="start">开始</n-button>
    <n-button v-if="running" type="primary" @click="stop">停止</n-button>
    <n-button v-if="wenhao < num && wenhao != 0 && !running" type="primary" @click="startBuchou">补抽</n-button>
    <n-button @click="reset">重置</n-button>
  </n-space>
</template>
<style>
.num {
  font-size: 32px;
  border: 2px solid #c43427;
  width: 80px;
  height: 80px;
  border-radius: 4px;
}
</style>
