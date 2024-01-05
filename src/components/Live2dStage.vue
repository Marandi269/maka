<template>
  <canvas id="canvas" style="margin-top: 50px;"></canvas>
  <p>音量: {{ volume}}</p>
</template>


<script setup>

import {defineProps, watchEffect} from 'vue';

// 使用 defineProps 声明 props
const props = defineProps({
  volume: Number
});

import * as PIXI from 'pixi.js';

import {Live2DModel} from 'pixi-live2d-display/cubism4';
import {onMounted} from "vue";

window.PIXI = PIXI;


async function render() {
  const app = new PIXI.Application({
    view: document.getElementById("canvas"),
    autoStart: true,
  });

  let cubism4Model = "assets/haru/haru_greeter_t03.model3.json";
  const model = await Live2DModel.from(cubism4Model);
  console.log('model:', model);
  app.stage.addChild(model);

  model.x = 800;
  model.y = 0;
  model.rotation = Math.PI;
  model.skew.x = Math.PI;
  model.scale.set(0.45);

  watchEffect(()=> {
    model.internalModel.coreModel.setParameterValueById('ParamMouthOpenY', props.volume);
  });
}

onMounted(render);
</script>

<style>
#live2d-canvas {
  width: 100%;
  height: 100%;
}
</style>
