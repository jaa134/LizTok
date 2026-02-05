<script setup lang="ts">
  /* Imports //////////////////////////////////////////////////////////////////////////////////////////////////////// */

  import 'sparklefall/src/sparklefall.css';

  import Button from 'primevue/button';
  import Image from 'primevue/image';
  import SparkleFall from 'sparklefall';
  import { onUnmounted } from 'vue';

  /* Emits ////////////////////////////////////////////////////////////////////////////////////////////////////////// */

  const emit = defineEmits<{
    (event: 'start-watching'): void;
  }>();

  /* Sparkles /////////////////////////////////////////////////////////////////////////////////////////////////////// */

  const sparkles = new SparkleFall({
    interval: 200,
    wind: 0,
    maxSparkles: 50,
    minSize: 10,
    maxSize: 20,
    sparkles: ['✨', '⭐', '💫', '🌟'],
    colors: ['rgba(255', '255', '255', '0.8)'],
  });

  onUnmounted(() => {
    sparkles.destroy();
  });
</script>

<template>
  <div class="home-page">
    <Image
      class="logo"
      src="/images/logo.webp"
      alt="Logo"
      width="100%"
    />
    <div class="button-container">
      <Button
        class="start-watching-button"
        label="Start watching"
        icon="pi pi-video"
        size="large"
        variant="outlined"
        @click="emit('start-watching')"
      />
    </div>
  </div>
</template>

<style scoped>
  @keyframes fadeIn {
    0% {
      opacity: 0;
    }
    100% {
      opacity: 1;
    }
  }

  @keyframes pulse {
    0% {
      transform: scale(1);
    }
    50% {
      transform: scale(1.1);
    }
    100% {
      transform: scale(1);
    }
  }

  .home-page {
    height: 100%;
    width: 100%;
    position: relative;
  }

  .logo {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    display: flex;
    justify-content: center;
    align-items: center;
    animation: fadeIn 1s ease-in-out;
  }

  .button-container {
    position: absolute;
    bottom: 75px;
    left: 0;
    right: 0;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .start-watching-button {
    animation: pulse 3s infinite;
  }
</style>
