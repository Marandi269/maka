<script setup>
import Live2dStage from "./components/Live2dStage.vue";

import {ref, onMounted} from 'vue';

const audioContext = new AudioContext();
const volume = ref(0);

const playAudio = () => {
  // 这里需要有一个音频文件的URL
  const audioUrl = '/M500004BrIpr2StDtD.mp3';
  // 使用 Fetch API 加载音频文件
  fetch(audioUrl)
      .then(response => response.arrayBuffer())
      .then(arrayBuffer => audioContext.decodeAudioData(arrayBuffer))
      .then(audioBuffer => {
        const source = audioContext.createBufferSource();
        source.buffer = audioBuffer;
        source.connect(audioContext.destination);
        source.start();

        // 创建分析器并连接
        const analyser = audioContext.createAnalyser();
        source.connect(analyser);

        // 分析音量
        analyseVolume(analyser);
      });
};

const analyseVolume = (analyser) => {
  const dataArray = new Uint8Array(analyser.frequencyBinCount);

  const analyse = () => {
    analyser.getByteFrequencyData(dataArray);

    // 计算音量
    let sum = 0;
    for (let i = 0; i < dataArray.length; i++) {
      sum += dataArray[i];
    }
    const average = sum / dataArray.length;
    console.log('avarage:', average);
    // 将音量映射到 0-1 的范围
    volume.value = Math.min(average * 2 / 255, 0.85);

    // 持续更新
    requestAnimationFrame(analyse);
  };

  analyse();
};
</script>

<template>
  <h1 class="text-cyan-200">hello</h1>
  <live2d-stage :volume="volume"/>
  <div>
    <button class="bg-green-500 hover:bg-green-700 text-white font-bold py-3 px-6 rounded-lg shadow" @click="playAudio">
      播放音频
    </button>
    <div>当前音量: {{ volume }}</div>
  </div>
</template>

