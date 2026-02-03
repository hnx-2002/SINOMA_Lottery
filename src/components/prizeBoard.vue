<script setup>
import { NTabPane, NTabs } from 'naive-ui';
import FlipGrid from './flipGrid.vue';

const prizeNames = [
  '花花加湿器',
  '拖把组合',
  '按压冰格',
  '腕力球',
  '充气颈枕',
  '鸡蛋收纳盒',
  '可调节颈托',
  '多功能日历',
  '置物架',
  '咖啡打泡器',
  '硅胶手托',
  '折叠盆组合',
  '蒸汽眼罩',
  'DIY鳌鱼灯',
  '多肉带盆',
  '手指篮球',
  '拉力带',
  '自动开合油壶',
  '六面魔豆',
  '5合1杯刷',
];
</script>

<template>
  <n-tabs type="line" animated justify-content="space-evenly" size="large">
    <n-tab-pane name="third" tab="三等奖">
      <flip-grid title="三等奖翻牌（5×6）" :cols="5" :rows="6" storage-key="prize-grid-third" :prize-names="prizeNames" />
    </n-tab-pane>
    <n-tab-pane name="second" tab="二等奖">
      <flip-grid title="二等奖翻牌（4×5）" :cols="4" :rows="5" storage-key="prize-grid-second" :prize-names="prizeNames" />
    </n-tab-pane>
    <n-tab-pane name="first" tab="一等奖">
      <flip-grid title="一等奖翻牌（2×5）" :cols="2" :rows="5" storage-key="prize-grid-first" :prize-names="prizeNames" />
    </n-tab-pane>
  </n-tabs>
</template>

