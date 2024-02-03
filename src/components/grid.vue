<script setup>
import { NGrid, NGi, NButton } from 'naive-ui';
import { useStorage } from '@vueuse/core';
import { ref } from 'vue';
const names = useStorage(
  'my-store',
  [
    {
      name: '置物架',
      active: false,
    },
    {
      name: 'DIY鳌鱼灯',
      active: false,
    },
    {
      name: '多肉带盆',
      active: false,
    },
    {
      name: '手指篮球',
      active: false,
    },
    {
      name: '折叠盆组合',
      active: false,
    },
    {
      name: '按压冰格',
      active: false,
    },
    {
      name: '多功能日历',
      active: false,
    },
    {
      name: '拉力带',
      active: false,
    },
    {
      name: '蒸汽眼罩',
      active: false,
    },
    {
      name: '六面魔豆',
      active: false,
    },
    {
      name: '腕力球',
      active: false,
    },
    {
      name: '充气颈枕',
      active: false,
    },
    {
      name: '花花加湿器',
      active: false,
    },
    {
      name: '拖把组合',
      active: false,
    },
    {
      name: '咖啡打泡器',
      active: false,
    },
    {
      name: '硅胶手托',
      active: false,
    },
    {
      name: '鸡蛋收纳盒',
      active: false,
    },
    {
      name: '可调节颈拖',
      active: false,
    },
    {
      name: '5合1杯刷',
      active: false,
    },
    {
      name: '自动开合油壶',
      active: false,
    },
  ],
  sessionStorage
);
function reset() {
  names.value.forEach((i) => {
    i.active = false;
  });
}
</script>
<template>
  <n-grid x-gap="12" :cols="5" y-gap="12" style="width: 80%; margin: 0 auto">
    <n-gi v-for="(item, index) in names">
      <div class="grid-item">
        <div v-if="item.active" class="gift">
          <div>{{ item.name }}</div>
          <div>{{ index + 1 }}</div>
        </div>
        <div v-else class="big-number" @click="item.active = true">{{ index + 1 }}</div>
      </div>
    </n-gi>
  </n-grid>
  <div style="text-align: center">
    <n-button type="primary" style="margin-top: 17px; margin-bottom: 5px" @click="reset">重置</n-button>
  </div>
</template>
<style scoped>
.grid-item {
  background-color: #eaf2ff;
  border: 1px solid #2b78ea;
  aspect-ratio: 1/1;
  border-radius: 4px;
}
.big-number {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: bold;
  font-size: 40px;
  cursor: pointer;
}
.gift {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  cursor: no-drop;
}
</style>
