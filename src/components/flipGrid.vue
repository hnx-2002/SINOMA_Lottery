<script setup>
import { NButton, NFlex, NSpace } from 'naive-ui';
import { computed } from 'vue';
import { useStorage } from '@vueuse/core';

const DEFAULT_PLACEHOLDER_NAME = '谢谢参与';

const props = defineProps({
  cols: {
    type: Number,
    required: true,
  },
  rows: {
    type: Number,
    required: true,
  },
  title: {
    type: String,
    required: true,
  },
  storageKey: {
    type: String,
    required: true,
  },
  prizes: {
    type: Array,
    required: true,
  },
  imgDir: {
    type: String,
    required: true,
  },
  placeholderName: {
    type: String,
    default: DEFAULT_PLACEHOLDER_NAME,
  },
});

function shuffleInPlace(arr) {
  for (let i = arr.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [arr[i], arr[j]] = [arr[j], arr[i]];
  }
  return arr;
}

const cellCount = computed(() => props.rows * props.cols);

function buildCells() {
  const normalized = (props.prizes || [])
    .map((p) => ({
      name: typeof p?.name === 'string' ? p.name : '',
      count: typeof p?.count === 'number' && Number.isFinite(p.count) ? Math.max(0, Math.floor(p.count)) : 0,
    }))
    .filter((p) => p.name && p.count > 0);

  const pool = [];
  normalized.forEach((p) => {
    for (let i = 0; i < p.count; i++) pool.push(p.name);
  });

  if (pool.length < cellCount.value) {
    const missing = cellCount.value - pool.length;
    for (let i = 0; i < missing; i++) pool.push(props.placeholderName);
  } else if (pool.length > cellCount.value) {
    pool.length = cellCount.value;
  }

  const shuffled = shuffleInPlace([...pool]);
  return shuffled.map((name) => ({
    name,
    revealed: false,
  }));
}

const cells = useStorage(props.storageKey, buildCells(), sessionStorage);

function ensureShape() {
  if (!Array.isArray(cells.value) || cells.value.length !== cellCount.value) {
    cells.value = buildCells();
  }
}

ensureShape();

function reshuffle() {
  cells.value = buildCells();
}

function resetReveal() {
  ensureShape();
  cells.value = cells.value.map((c) => ({ ...c, revealed: false }));
}

function reveal(index) {
  const cell = cells.value[index];
  if (!cell || cell.revealed) return;
  cells.value[index] = { ...cell, revealed: true };
}

function buildPublicImgUrl({ rootDir, prizeDir, name, ext }) {
  const encoded = encodeURIComponent(name);
  return `${import.meta.env.BASE_URL}${rootDir}/${prizeDir}/${encoded}.${ext}`;
}

function imgUrl(name) {
  return buildPublicImgUrl({ rootDir: 'img', prizeDir: props.imgDir, name, ext: 'png' });
}

function fallbackImgUrls(name) {
  return [
    buildPublicImgUrl({ rootDir: 'image', prizeDir: props.imgDir, name, ext: 'png' }),
    buildPublicImgUrl({ rootDir: 'img', prizeDir: props.imgDir, name, ext: 'jpg' }),
    buildPublicImgUrl({ rootDir: 'image', prizeDir: props.imgDir, name, ext: 'jpg' }),
  ];
}

function isPlaceholder(name) {
  return name === props.placeholderName;
}

function handleImgError(e, name) {
  if (isPlaceholder(name)) return;
  const img = e?.target;
  if (!img) return;

  const candidates = [imgUrl(name), ...fallbackImgUrls(name)];

  const index = Number.parseInt(img.dataset?.fallbackIndex ?? '0', 10);
  const nextIndex = Number.isFinite(index) ? index + 1 : 1;
  const next = candidates[nextIndex];
  if (!next) return;

  img.dataset.fallbackIndex = String(nextIndex);
  img.src = next;
}
</script>

<template>
  <div class="wrap">
    <n-flex justify="space-between" align="center" style="padding: 0 12px; flex-wrap: wrap; gap: 12px">
      <div class="title">{{ title }}</div>
      <n-space>
        <n-button @click="resetReveal">重置翻牌</n-button>
        <n-button type="primary" @click="reshuffle">重新打乱</n-button>
      </n-space>
    </n-flex>

    <div class="grid" :style="{ gridTemplateColumns: `repeat(${cols}, 1fr)` }">
      <div v-for="(cell, index) in cells" :key="index" class="cell" @click="reveal(index)">
        <div class="card" :class="{ revealed: cell.revealed }">
          <div class="inner">
            <div class="face front">
              <div class="num">{{ index + 1 }}</div>
            </div>
            <div
              class="face back"
              :class="{ placeholder: isPlaceholder(cell.name) }"
            >
              <img
                v-if="!isPlaceholder(cell.name)"
                class="img"
                :src="imgUrl(cell.name)"
                :alt="cell.name"
                data-fallback-index="0"
                @error="(e) => handleImgError(e, cell.name)"
              />
              <div class="name">{{ cell.name }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.wrap {
  padding: 12px 12px 20px;
}
.title {
  font-size: 18px;
  font-weight: 700;
  color: #333;
}
.grid {
  display: grid;
  gap: 10px;
  width: 60%;
  margin: 14px auto 0;
}
.cell {
  aspect-ratio: 1/1;
  cursor: pointer;
}
.card {
  width: 100%;
  height: 100%;
  perspective: 800px;
}
.inner {
  width: 100%;
  height: 100%;
  position: relative;
  transform-style: preserve-3d;
  transition: transform 0.6s ease;
}
.card.revealed .inner {
  transform: rotateY(180deg);
}
.face {
  position: absolute;
  inset: 0;
  backface-visibility: hidden;
  border-radius: 6px;
  overflow: hidden;
  border: 1px solid #c43427;
}
.front {
  background-color: #ffe9e7;
  display: flex;
  align-items: center;
  justify-content: center;
}
.num {
  font-size: 20px;
  font-weight: 800;
  color: #c43427;
}
.back {
  transform: rotateY(180deg);
  display: flex;
  align-items: flex-end;
  justify-content: center;
}
.img {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  z-index: 0;
}
.back.placeholder {
  background: #f7f7f7;
  display: flex;
  align-items: center;
  justify-content: center;
}
.back.placeholder .name {
  background: transparent;
  color: #333;
  font-weight: 700;
}
.name {
  width: 100%;
  text-align: center;
  padding: 8px 6px;
  color: #fff;
  background: rgba(0, 0, 0, 0.6);
  font-size: 14px;
  z-index: 1;
  position: relative;
}
</style>
