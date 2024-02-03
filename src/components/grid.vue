<script setup>
import { NGrid, NGi, NButton } from 'naive-ui';
import { useStorage } from '@vueuse/core';
import { ref } from 'vue';
const names = useStorage(
  'my-store',
  [
    {
      name: '花花加湿器',
      active: false,
    },
    {
      name: '拖把组合',
      active: false,
    },
    {
      name: '按压冰格',
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
      name: '鸡蛋收纳盒',
      active: false,
    },
    {
      name: '可调节颈托',
      active: false,
    },
    {
      name: '多功能日历',
      active: false,
    },
    {
      name: '置物架',
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
      name: '折叠盆组合',
      active: false,
    },
    {
      name: '蒸汽眼罩',
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
      name: '拉力带',
      active: false,
    },
    {
      name: '自动开合油壶',
      active: false,
    },
    {
      name: '六面魔豆',
      active: false,
    },

    {
      name: '5合1杯刷',
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
        <transition>
          <div
            v-if="item.active"
            class="gift"
            :style="{
              'background-image': `url(/szsnh/img/${item.name}.jpg)`,
            }"
          >
            <div class="gift-name">{{ item.name }}</div>
          </div>
          <div v-else class="big-number" @click="item.active = true">{{ index + 1 }}</div>
        </transition>
      </div>
    </n-gi>
  </n-grid>
  <div style="text-align: center">
    <n-button type="primary" style="margin-top: 16px; margin-bottom: 5px" @click="reset">重置</n-button>
  </div>
</template>
<style scoped>
.v-enter-active,
.v-leave-active {
  transition: opacity 0.5s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
.grid-item {
  background-color: #ffe9e7;
  border: 1px solid #c43427;
  aspect-ratio: 1/1;
  border-radius: 4px;
  overflow: hidden;
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
  background-size: cover;
  border-radius: 4px;
}
.gift-name {
  background-color: rgba(0, 0, 0, 0.6);
  color: #fff;
  width: 100%;
  text-align: center;
}
</style>
