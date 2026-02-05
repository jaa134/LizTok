<script setup lang="ts">
  /* Imports //////////////////////////////////////////////////////////////////////////////////////////////////////// */

  import Button from 'primevue/button';
  import Image from 'primevue/image';

  /* Emits ////////////////////////////////////////////////////////////////////////////////////////////////////////// */

  const emit = defineEmits<{
    (event: 'restart'): void;
  }>();
</script>

<template>
  <div class="credits-page">
    <div class="text">
      <div>To one of the most talented people I know.</div>
      <div>Happy birthday, Liz!</div>
      <div><strong>RIP 20's!!!</strong></div>
    </div>
    <div class="button-container">
      <Button
        label="Restart"
        icon="pi pi-refresh"
        @click="emit('restart')"
      />
    </div>
  </div>
</template>

<style scoped>
  .credits-page {
    position: relative;
    height: 100%;
    width: 100%;
    background-image: url('/images/friendship.webp');
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
  }

  .text {
    position: absolute;
    bottom: 70px;
    left: 20px;
    right: 20px;
    padding: 10px;
    text-align: center;
    font-size: 14px;
    line-height: 1.5;
    background-color: rgba(0, 0, 0, 0.75);
    border-radius: 20px;

    :last-child {
      font-size: 32px;
      margin-top: 10px;
    }
  }

  .button-container {
    position: absolute;
    bottom: 20px;
    left: 0;
    right: 0;
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>
