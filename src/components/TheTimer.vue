<script setup lang="ts">
import ConfettiExplosion from './ConfettiExplosion.vue';

const props = defineProps<{
  time: string;
  showConfettiExplosion: boolean;
  confettiWidth: number;
  confettiHeight: number;
}>();
</script>

<template>
  <main>
    <div>
      {{ props.time }}
    </div>

    <div style="position: absolute; top: 50%; left: 50%">
      <ConfettiExplosion
        v-if="props.showConfettiExplosion"
        :stage-height="confettiHeight"
        :stage-width="confettiWidth"
      ></ConfettiExplosion>
    </div>
  </main>
</template>

<style scoped>
main {
  display: flex;
  width: 100%;
  align-items: center;
  justify-content: center;
  background-color: black;
  color: white;
  font-family: 'Courier New', Courier, monospace;

  position: relative;
}
</style>
