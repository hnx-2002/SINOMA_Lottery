<script setup>
import { NTabPane, NTabs } from 'naive-ui';
import FlipGrid from './flipGrid.vue';

// 奖品配置（互斥）：每一类单独配置 name + count，总数应等于网格格子数（不够会自动用“谢谢参与”补满）。
const thirdPrizes = [
  { name: '笔记本套装', count: 4 },
  { name: '出差旅行包', count: 4 },
  { name: '磁吸门贴', count: 4 },
  { name: '读卡器', count: 4 },
  { name: '手机支架', count: 4 },
  { name: '小夜灯', count: 3 },
  { name: '整齐眼罩', count: 3 },
  { name: '自拍神器', count: 4 },
];

const secondPrizes = [
  { name: '88VIP', count: 3 },
  { name: '儿童手工', count: 3 },
  { name: '充电宝', count: 3 },
  { name: '折叠水壶', count: 3 },
  { name: '泡泡玛特梦里梦外系列', count: 3 },
  { name: '泡泡玛特餐桌系列', count: 3 },
  { name: '糖果包黏土', count: 2 },
];

const firstPrizes = [
  { name: '100WGaN充电头', count: 2 },
  { name: 'Ai鼠标', count: 2 },
  { name: '三折叠键盘', count: 2 },
  { name: '奇趣滨海套装', count: 1 },
  { name: '磁力积木高铁', count: 1 },
  { name: '迷你遥控飞机', count: 1 },
  { name: '除螨仪', count: 1 },
];
</script>

<template>
  <n-tabs type="line" animated justify-content="space-evenly" size="large">
    <n-tab-pane name="third" tab="三等奖">
      <flip-grid title="三等奖翻牌（5×6）" :cols="6" :rows="5" storage-key="prize-grid-third" img-dir="third" :prizes="thirdPrizes" />
    </n-tab-pane>
    <n-tab-pane name="second" tab="二等奖">
      <flip-grid title="二等奖翻牌（4×5）" :cols="5" :rows="4" storage-key="prize-grid-second" img-dir="second" :prizes="secondPrizes" />
    </n-tab-pane>
    <n-tab-pane name="first" tab="一等奖">
      <flip-grid title="一等奖翻牌（2×5）" :cols="5" :rows="2" storage-key="prize-grid-first" img-dir="first" :prizes="firstPrizes" />
    </n-tab-pane>
  </n-tabs>
</template>
