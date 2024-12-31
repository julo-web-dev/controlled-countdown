<script setup lang="ts">
import { computed, ref, watch } from 'vue';
import TheTimer from './TheTimer.vue';
import NewWindow from './NewWindow.vue';

const openWindow = ref(false);
const startTime = ref('00:00:00');
function updateTime() {
  const [hours, minutes, seconds] = startTime.value.split(':').map(Number);
  time.value = (hours * 3600 + minutes * 60 + seconds) * 1000;
}
watch(startTime, updateTime);

const showMs = ref(false);
const showH = computed(() => startTime.value.split(':').map(Number)[0] > 0);
const finalTime = ref<Date | undefined>(undefined);
const remainingMs = ref<number | undefined>(undefined);

const time = ref(0);
const formatedTime = computed(() => {
  let visualTime = time.value;
  if (!showMs.value) visualTime = Math.ceil(time.value / 1000) * 1000; // We want the countdown to end at exactly 0.

  const date = new Date(visualTime);
  return `${date.toLocaleTimeString('en-US', {
    timeZone: 'UTC',
    hour: showH.value ? '2-digit' : undefined,
    minute: '2-digit',
    second: '2-digit',
    hour12: false,
  })}${showMs.value ? '.' + date.getMilliseconds().toString().padStart(3, '0') : ''}`;
});

const animationFn = () => {
  const t = finalTime.value;
  if (t) {
    const remTime = t.getTime() - Date.now();
    if (remTime < 0) {
      resetTimer();
      time.value = 0;
    } else {
      time.value = remTime;
    }
  }
  requestAnimationFrame(animationFn);
};
requestAnimationFrame(animationFn);

function start() {
  finalTime.value = new Date();
  finalTime.value.setMilliseconds(finalTime.value.getMilliseconds() + time.value);
}

function pause() {
  remainingMs.value = (finalTime.value?.getTime() || 0) - Date.now();
  finalTime.value = undefined;
}

function resume() {
  finalTime.value = new Date(Date.now() + (remainingMs.value || 0));
  remainingMs.value = undefined;
}

function resetTimer() {
  finalTime.value = undefined;
  remainingMs.value = undefined;

  updateTime();
}
</script>
<template>
  <div class="container">
    <h1>Timer Configuration</h1>

    <label for="timeInput">Start (hh:mm:ss):</label>
    <input type="time" id="timeInput" step="1" v-model="startTime" />

    <br />

    <button v-if="finalTime !== undefined" @click="pause()">Pause</button>
    <button v-else-if="remainingMs !== undefined" @click="resume()">Resume</button>
    <button v-else @click="start()">Start</button>
    <button id="resetBtn" @click="resetTimer()">Reset</button>

    <button @click="showMs = !showMs">Show milliseconds</button>

    <div id="timerDisplay">{{ formatedTime }}</div>

    <button v-if="!openWindow" @click="openWindow = true">Open popup</button>
    <button v-else @click="openWindow = false">Close popup</button>
  </div>

  <NewWindow v-model="openWindow">
    <TheTimer :time="formatedTime" :show-confetti-explosion="time === 0"></TheTimer>
  </NewWindow>
</template>
