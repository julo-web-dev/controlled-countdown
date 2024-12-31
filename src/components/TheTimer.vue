<script setup lang="ts">
const props = defineProps<{ time: string }>();
</script>

<template>
  <main>
    <div>
      {{ props.time }}
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
