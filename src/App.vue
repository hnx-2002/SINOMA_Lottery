<script setup>
import { NTabs, NTabPane, NCard, NConfigProvider } from 'naive-ui';
import grid from './components/grid.vue';
import lucky from './components/lucky.vue';
import { useFullscreen } from '@vueuse/core';
const { isFullscreen, toggle } = useFullscreen();
const theme = {
  common: {
    primaryColor: '#c43427',
    primaryColorHover: '#c64034',
    primaryColorPressed: '#b11f12',
    fontSize: '16px',
  },
  Card: {
    borderColor: '#e5e6eb',
  },
};
</script>

<template>
  <div class="title" @click="toggle">数字所年会抽奖系统</div>

  <n-config-provider :abstract="true" :theme-overrides="theme">
    <n-card
      style="
        width: 60%;
        margin: 0 auto;
        flex: 1;
        margin-bottom: 20px;
        box-shadow: 0 1px 2px -2px rgba(0, 0, 0, 0.32), 0 3px 6px 0 rgba(0, 0, 0, 0.24), 0 5px 12px 4px rgba(0, 0, 0, 0.16);
      "
      :content-style="{
        padding: 0,
      }"
    >
      <n-tabs type="line" animated justify-content="space-evenly" size="large">
        <n-tab-pane name="三等奖" tab="三等奖" display-directive="show">
          <lucky :num="5"></lucky>
        </n-tab-pane>
        <n-tab-pane name="二等奖" tab="二等奖" display-directive="show">
          <lucky :num="2"></lucky>
        </n-tab-pane>
        <n-tab-pane name="一等奖" tab="一等奖" display-directive="show">
          <lucky></lucky>
        </n-tab-pane>
      </n-tabs>
    </n-card>
  </n-config-provider>
</template>

<style>
html,
body,
#app {
  width: 100%;
  height: 100%;
  margin: 0;
}
#app {
  display: flex;
  flex-direction: column;
  background-image: url(./assets/bg.jpg);
  background-size: cover;
}
.title {
  text-align: center;
  font-size: 2em;
  font-weight: bold;
  line-height: 2.25;
  color: #ece7b6;
  letter-spacing: 2px;
}
</style>
