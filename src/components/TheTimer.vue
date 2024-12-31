<script setup lang="ts">
import ConfettiExplosion from './ConfettiExplosion.vue';

const props = defineProps<{
  time: string;
  showConfettiExplosion: boolean;
  confettiWidth: number;
  confettiHeight: number;
  textColor: string;
  backgroundColor: string;
  fontSize: number;
}>();
</script>

<template>
  <main
    :style="{
      backgroundColor: backgroundColor,
      color: textColor,
      fontSize: `${fontSize}em`,
    }"
  >
    <div>
      {{ props.time }}
    </div>

    <div style="position: absolute; top: 50%; left: 50%">
      <ConfettiExplosion
        v-if="props.showConfettiExplosion"
        :stage-height="confettiHeight"
        :stage-width="confettiWidth"
        :force="1"
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
  font-family: 'Courier New', Courier, monospace;

  position: relative;
}
</style>
