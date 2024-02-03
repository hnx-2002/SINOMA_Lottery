<script setup>
import { NButton, NSpace } from 'naive-ui';
import { ref, computed } from 'vue';
import { useStorage } from '@vueuse/core';
const i = ref(0);
const running = ref(false);
const stopping = ref(false);
const names = ref([
  '胡亚东',
  '郭玲玲',
  '宋子健',
  '魏子森',
  '徐凤娇',
  '高杰',
  '黄宇姗',
  '林默菁',
  '孙鑫',
  '贾薇',
  '孔庆森',
  '李彪彪',
  '李朦',
  '刘立东',
  '张明',
  '段金倩',
  '梁小龙',
  '刘子豪',
  '王灵剑',
  '王涛',
  '郑毅',
  '高志涛',
  '常亮',
  '张明生',
  '陈云宝',
  '白孟宸',
  '程明',
  '陈友良',
  '董晓晨',
  '董英杰',
  '董运涛',
  '段然',
  '冯永涛',
  '耿宁宁',
  '郭晋晋',
  '郭可骍',
  '郭庆杰',
  '郝晓璐',
  '侯立民',
  '黄发义',
  '解鑫',
  '季可越',
  '梁胜松',
  '栗红玉',
  '李杰',
  '李君言',
  '蔺珂菲',
  '刘航宇',
  '刘清',
  '李文鑫',
  '鲁冰',
  '卢鑫',
  '孟庆尧',
  '么伟',
  '彭妙培',
  '任潇潇',
  '邵雪青',
  '沈磊',
  '史明强',
  '孙利波',
  '孙司羿',
  '孙亚超',
  '王倩',
  '王荣',
  '王汝贤',
  '王威',
  '王兴涛',
  '吴其伟',
  '杨超',
  '杨帆',
  '杨光',
  '伊丁',
  '尹成林',
  '于海超',
  '余智杰',
  '张鹏迪',
  '张歆',
  '张杨',
  '张宇康',
  '赵静',
  '郑彬',
  '郑贺',
  '周敬涛',
  '李文轩',
  '韦勇',
  '刘松',
  '张振宇',
  '王青松',
  '张闯',
  '郑明',
  '李长俊',
  '明坤坤',
  '杨昕坡',
]);
const honor = useStorage('honor', [], sessionStorage);
let timer = null;
function start() {
  running.value = true;
  timer = setInterval(() => {
    i.value++;
    if (i.value == names.value.length) {
      i.value = 0;
    }
  }, 80);
}
const index = ref(0);
function stop() {
  index.value = makeNum(0, names.value.length - 1);
  honor.value.push(names.value[index.value]);
  stopping.value = true;
  running.value = false;
  clearInterval(timer);
}
const showText = computed(() => {
  if (running.value) {
    return names.value[i.value];
  } else if (stopping.value) {
    return names.value[index.value];
  } else {
    return '等待开始';
  }
});
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
  <div style="font-size: 60px; text-align: center; margin-bottom: 10px">{{ showText }}</div>

  <n-space justify="center">
    <n-button v-if="!running" type="primary" @click="start">开始</n-button>
    <n-button v-else type="primary" @click="stop">停止</n-button>
    <n-button @click="reset">重置</n-button>
  </n-space>
  <div style="text-align: center; font-size: 22px; line-height: 1; margin-top: 30px">
    <div style="font-weight: bold">幸运儿名单</div>
    <p v-for="item in honor">{{ item }}</p>
  </div>
</template>
