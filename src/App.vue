<script setup>
import { NCard, NConfigProvider, NMessageProvider } from 'naive-ui';
import lucky from './components/lucky.vue';
import { useFullscreen } from '@vueuse/core';
const { toggle } = useFullscreen();
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
    <n-message-provider>
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
        <lucky />
      </n-card>
    </n-message-provider>
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
