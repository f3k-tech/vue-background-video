<template>
  <div class="background-video">
    <video autoplay muted loop class="video-element" :class="anchorClass" preload="auto" ref="videoElement">
      <template v-for="(src, index) in videos" :key="index">
        <source :src="getVideoPath(src)" type="video/mp4">
      </template>
      Your browser does not support the video tag.
    </video>
    <div class="overlay">
      <slot></slot>
    </div>
  </div>
</template>

<script setup lang="ts">
import { defineProps, computed, ref, onMounted } from 'vue';

const props = defineProps<{
  anchor: 'center' | 'left' | 'right' | 'top' | 'bottom';
  videos: string[];
}>();

const getVideoPath = (src: string) => {
  return new URL(`${src}`, import.meta.url).href;
};

const videoElement = ref<HTMLVideoElement | null>(null);

const anchorClass = computed(() => {
  switch (props.anchor) {
    case 'left':
      return 'anchor-left';
    case 'right':
      return 'anchor-right';
    case 'top':
      return 'anchor-top';
    case 'bottom':
      return 'anchor-bottom';
    default:
      return 'anchor-center';
  }
});

onMounted(() => {
  if (videoElement.value) {
    videoElement.value.addEventListener('ended', () => {
      videoElement.value?.play();
    });
  }
});
</script>

<style scoped>
.background-video {
  position: relative;
  width: 100%;
  height: 100vh;
  overflow: hidden;
}

.video-element {
  position: absolute;
  width: 100%;
  height: 100%;
  object-fit: cover;
  z-index: -1;
}

.anchor-center {
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}

.anchor-left {
  top: 50%;
  left: 0;
  transform: translateY(-50%);
}

.anchor-right {
  top: 50%;
  right: 0;
  transform: translateY(-50%);
}

.anchor-top {
  top: 0;
  left: 50%;
  transform: translateX(-50%);
}

.anchor-bottom {
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
}

.overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
