<script setup>
import { NButton, NFlex, NInputNumber, NSpace, useMessage } from 'naive-ui';
import { computed, ref, watch } from 'vue';
import { useStorage } from '@vueuse/core';
const message = useMessage();

const MAX_NUMBER = 62;
const allNumbers = Array.from({ length: MAX_NUMBER }, (_, i) => i + 1);

const PRIZE_QUOTA = {
  special: 2,
  third: 30,
  second: 20,
  first: 10,
};

const specialCount = useStorage('specialCount', 0, sessionStorage);
const thirdCount = useStorage('thirdCount', 0, sessionStorage);
const secondCount = useStorage('secondCount', 0, sessionStorage);
const firstCount = useStorage('firstCount', 0, sessionStorage);

const running = ref(false);
let timer = null;

const results = ref({
  special: [],
  third: [],
  second: [],
  first: [],
});

const display = ref({
  special: [],
  third: [],
  second: [],
  first: [],
});

const drawnNumbers = useStorage('drawnNumbers', [], sessionStorage);
const drawnByPrize = useStorage(
  'drawnByPrize',
  {
    special: [],
    third: [],
    second: [],
    first: [],
  },
  sessionStorage
);

{
  const prizeTotal =
    (drawnByPrize.value.special?.length ?? 0) +
    (drawnByPrize.value.third?.length ?? 0) +
    (drawnByPrize.value.second?.length ?? 0) +
    (drawnByPrize.value.first?.length ?? 0);
  if ((drawnNumbers.value?.length ?? 0) > 0 && prizeTotal === 0) {
    message.warning('检测到旧版本抽取记录（无法区分奖项），建议点击“重置全部”。');
  }
}

const remainingByPrize = computed(() => ({
  special: Math.max(0, PRIZE_QUOTA.special - (drawnByPrize.value.special?.length ?? 0)),
  third: Math.max(0, PRIZE_QUOTA.third - (drawnByPrize.value.third?.length ?? 0)),
  second: Math.max(0, PRIZE_QUOTA.second - (drawnByPrize.value.second?.length ?? 0)),
  first: Math.max(0, PRIZE_QUOTA.first - (drawnByPrize.value.first?.length ?? 0)),
}));

function clampCount(value) {
  if (typeof value !== 'number' || Number.isNaN(value)) return 0;
  return Math.max(0, Math.min(MAX_NUMBER, Math.floor(value)));
}

function shuffleInPlace(arr) {
  for (let i = arr.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [arr[i], arr[j]] = [arr[j], arr[i]];
  }
  return arr;
}

function clearIntervalSafe() {
  if (timer) clearInterval(timer);
  timer = null;
}

function clearResults() {
  results.value = { special: [], third: [], second: [], first: [] };
}

function clearDisplay(counts) {
  display.value = {
    special: Array.from({ length: counts.special }, () => '?'),
    third: Array.from({ length: counts.third }, () => '?'),
    second: Array.from({ length: counts.second }, () => '?'),
    first: Array.from({ length: counts.first }, () => '?'),
  };
}

function getCounts() {
  const special = clampCount(specialCount.value);
  const third = clampCount(thirdCount.value);
  const second = clampCount(secondCount.value);
  const first = clampCount(firstCount.value);
  return { special, third, second, first, need: special + first + second + third };
}

function pickNumbers(available, counts) {
  const shuffled = shuffleInPlace([...available]);
  const picked = shuffled.slice(0, counts.need);
  const specialRes = picked.slice(0, counts.special);
  const firstStart = counts.special;
  const firstRes = picked.slice(firstStart, firstStart + counts.first);
  const secondStart = firstStart + counts.first;
  const secondRes = picked.slice(secondStart, secondStart + counts.second);
  const thirdStart = secondStart + counts.second;
  const thirdRes = picked.slice(thirdStart, thirdStart + counts.third);
  return {
    picked,
    specialRes,
    firstRes,
    secondRes,
    thirdRes,
  };
}

function validateCounts(counts, availableCount) {
  if (counts.need <= 0) {
    message.warning('请先输入本轮要抽取的人数。');
    return false;
  }
  if (
    counts.special > remainingByPrize.value.special ||
    counts.third > remainingByPrize.value.third ||
    counts.second > remainingByPrize.value.second ||
    counts.first > remainingByPrize.value.first
  ) {
    message.error(
      `本轮抽取人数超过剩余名额：特等奖剩余 ${remainingByPrize.value.special}，一等奖剩余 ${remainingByPrize.value.first}，二等奖剩余 ${remainingByPrize.value.second}，三等奖剩余 ${remainingByPrize.value.third}。`
    );
    return false;
  }
  if (availableCount < counts.need) {
    message.error(`剩余可抽人数不足（剩余 ${availableCount}，本轮需要 ${counts.need}）。请先重置或减少人数。`);
    return false;
  }
  return true;
}

function start() {
  if (running.value) return;

  const counts = getCounts();
  const available = allNumbers.filter((n) => !drawnNumbers.value.includes(n));
  if (!validateCounts(counts, available.length)) return;

  clearResults();
  clearDisplay(counts);

  running.value = true;
  timer = setInterval(() => {
    const preview = pickNumbers(available, counts);
    display.value = {
      special: preview.specialRes,
      third: preview.thirdRes,
      second: preview.secondRes,
      first: preview.firstRes,
    };
  }, 80);
}

function stop() {
  if (!running.value) return;

  running.value = false;
  clearIntervalSafe();

  const counts = getCounts();
  const available = allNumbers.filter((n) => !drawnNumbers.value.includes(n));
  if (!validateCounts(counts, available.length)) return;

  const finalPick = pickNumbers(available, counts);
  results.value = {
    special: finalPick.specialRes,
    third: finalPick.thirdRes,
    second: finalPick.secondRes,
    first: finalPick.firstRes,
  };
  display.value = {
    special: finalPick.specialRes,
    third: finalPick.thirdRes,
    second: finalPick.secondRes,
    first: finalPick.firstRes,
  };

  drawnNumbers.value.push(...finalPick.picked);
  drawnByPrize.value.special.push(...finalPick.specialRes);
  drawnByPrize.value.third.push(...finalPick.thirdRes);
  drawnByPrize.value.second.push(...finalPick.secondRes);
  drawnByPrize.value.first.push(...finalPick.firstRes);
}

function resetAll() {
  running.value = false;
  clearIntervalSafe();
  drawnNumbers.value = [];
  drawnByPrize.value = { special: [], third: [], second: [], first: [] };
  clearResults();
  clearDisplay({ special: 0, third: 0, second: 0, first: 0 });
  message.success('已重置本次会话的抽取记录。');
}

watch(
  () => [specialCount.value, thirdCount.value, secondCount.value, firstCount.value],
  () => {
    if (running.value) return;
    clearResults();
    clearDisplay(getCounts());
  },
  { immediate: true }
);
</script>
<template>
  <div style="padding: 24px 0">
    <n-flex justify="center" align="center" style="gap: 24px; padding: 16px 24px; flex-wrap: wrap">
      <n-flex vertical style="width: 160px">
        <div class="label">特等奖（个）</div>
        <n-input-number v-model:value="specialCount" :min="0" :max="MAX_NUMBER" :disabled="running" />
      </n-flex>
      <n-flex vertical style="width: 160px">
        <div class="label">三等奖（个）</div>
        <n-input-number v-model:value="thirdCount" :min="0" :max="MAX_NUMBER" :disabled="running" />
      </n-flex>
      <n-flex vertical style="width: 160px">
        <div class="label">二等奖（个）</div>
        <n-input-number v-model:value="secondCount" :min="0" :max="MAX_NUMBER" :disabled="running" />
      </n-flex>
      <n-flex vertical style="width: 160px">
        <div class="label">一等奖（个）</div>
        <n-input-number v-model:value="firstCount" :min="0" :max="MAX_NUMBER" :disabled="running" />
      </n-flex>
      <n-flex vertical style="width: 220px">
        <div class="meta">
          剩余可抽：特等奖 {{ remainingByPrize.special }} 个，三等奖 {{ remainingByPrize.third }} 个，二等奖 {{ remainingByPrize.second }} 个，一等奖
          {{ remainingByPrize.first }} 个
        </div>
      </n-flex>
    </n-flex>

    <n-space justify="center" style="margin: 8px 0 20px">
      <n-button v-if="!running" type="primary" @click="start">开始</n-button>
      <n-button v-else type="primary" @click="stop">停止</n-button>
      <n-button @click="resetAll">重置全部</n-button>
    </n-space>

    <n-flex vertical style="gap: 24px; padding: 0 24px 8px">
      <div v-if="display.special.length > 0">
        <div class="section-title">特等奖</div>
        <n-flex justify="center" style="flex-wrap: wrap; gap: 16px">
          <div v-for="(n, idx) in display.special" :key="`special-${idx}`" class="num">{{ n }}</div>
        </n-flex>
      </div>

      <div v-if="display.third.length > 0">
        <div class="section-title">三等奖</div>
        <n-flex justify="center" style="flex-wrap: wrap; gap: 16px">
          <div v-for="(n, idx) in display.third" :key="`third-${idx}`" class="num">{{ n }}</div>
        </n-flex>
      </div>

      <div v-if="display.second.length > 0">
        <div class="section-title">二等奖</div>
        <n-flex justify="center" style="flex-wrap: wrap; gap: 16px">
          <div v-for="(n, idx) in display.second" :key="`second-${idx}`" class="num">{{ n }}</div>
        </n-flex>
      </div>

      <div v-if="display.first.length > 0">
        <div class="section-title">一等奖</div>
        <n-flex justify="center" style="flex-wrap: wrap; gap: 16px">
          <div v-for="(n, idx) in display.first" :key="`first-${idx}`" class="num">{{ n }}</div>
        </n-flex>
      </div>
    </n-flex>
  </div>
</template>
<style>
.num {
  font-size: 32px;
  border: 2px solid #c43427;
  width: 80px;
  height: 80px;
  border-radius: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.label {
  color: #333;
  font-size: 14px;
  line-height: 1.8;
}
.meta {
  color: rgba(0, 0, 0, 0.75);
  font-size: 14px;
  line-height: 1.8;
}
.section-title {
  text-align: center;
  font-size: 18px;
  font-weight: 700;
  color: #333;
  margin-bottom: 10px;
}
</style>
