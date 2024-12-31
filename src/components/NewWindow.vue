<!-- Copied from https://stackoverflow.com/a/75305925/517914 and modified for better usability -->
<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount, watch, nextTick } from 'vue';

const model = defineModel({ default: false });
const width = defineModel('width', { default: 0 });
const height = defineModel('height', { default: 0 });

let windowRef: Window | null = null;
const portal = ref(null);

const copyStyles = (sourceDoc: Document, targetDoc: Document): void => {
  for (const styleSheet of Array.from(sourceDoc.styleSheets)) {
    if (styleSheet.cssRules) {
      // for <style> elements
      const nwStyleElement = sourceDoc.createElement('style');

      for (const cssRule of Array.from(styleSheet.cssRules)) {
        // write the text of each rule into the body of the style element
        nwStyleElement.append(sourceDoc.createTextNode(cssRule.cssText));
      }

      targetDoc.head.append(nwStyleElement);
    } else if (styleSheet.href) {
      // for <link> elements loading CSS from a URL
      const nwLinkElement = sourceDoc.createElement('link');

      nwLinkElement.rel = 'stylesheet';
      nwLinkElement.href = styleSheet.href;
      targetDoc.head.append(nwLinkElement);
    }
  }
};

const openPortal = (): void => {
  nextTick()
    .then((): void => {
      windowRef = window.open('', '', 'width=600,height=400,left=200,top=200');
      if (!windowRef || !portal.value) return;
      windowRef.document.body.append(portal.value);
      copyStyles(window.document, windowRef.document);
      windowRef.addEventListener('resize', resizePortal);
      windowRef.addEventListener('beforeunload', closePortal);
    })
    .catch((error: Error) => console.error('Cannot instantiate portal', error.message));
};

const resizePortal = (): void => {
  if (windowRef) {
    width.value = windowRef.innerWidth;
    height.value = windowRef.innerHeight;
  }
};

const closePortal = (): void => {
  if (windowRef) {
    windowRef.close();
    windowRef = null;
    model.value = false;
    width.value = 0;
    height.value = 0;
  }
};

watch(model, () => {
  if (model.value) {
    openPortal();
  } else {
    closePortal();
  }
});

onMounted(() => {
  if (model.value) {
    openPortal();
  }
});
onBeforeUnmount(() => {
  closePortal();
});

window.onbeforeunload = () => {
  closePortal();
};
</script>

<template>
  <div v-if="model" ref="portal" style="display: flex; width: 100%; height: 100%">
    <slot />
  </div>
</template>
