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
const i = ref(0);
const running = ref(false);
const stopping = ref(false);
const total = ref(70);
const showText = ref([]);
const honor = useStorage('honor', [], sessionStorage);
let timer = null;
function start() {
  running.value = true;
  timer = setInterval(() => {
    showText.value = [makeNum(0, 70), makeNum(0, 70), makeNum(0, 70), makeNum(0, 70), makeNum(0, 70)];
  }, 80);
}
const index = ref(0);
function stop() {
  showText.value = [makeNum(0, 70), makeNum(0, 70), makeNum(0, 70), makeNum(0, 70), makeNum(0, 70)];
  honor.value = showText.value;
  stopping.value = true;
  running.value = false;
  clearInterval(timer);
}

function reset() {
  stopping.value = false;
  running.value = false;
  honor.value = [];
  clearInterval(timer);
}
function makeNum(m, n) {
  return parseInt(Math.random() * (m - n + 1) + n);
}
</script>
<template>
  <n-flex justify="center">
    <div v-for="(item, index) in num" style="font-size: 24px; text-align: center; margin-bottom: 10px">
      {{ showText[index] }}
    </div>
  </n-flex>

  <n-space justify="center">
    <n-button v-if="!running" type="primary" @click="start">开始</n-button>
    <n-button v-else type="primary" @click="stop">停止</n-button>
    <n-button @click="reset">重置</n-button>
  </n-space>
  <div style="text-align: center; font-size: 22px; line-height: 1; margin-top: 30px">
    <div style="font-weight: bold">幸运儿名单</div>
    <n-flex justify="center">
      <p v-for="item in honor">{{ item }}</p>
    </n-flex>
  </div>
</template>
