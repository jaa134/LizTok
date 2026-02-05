<script setup lang="ts">
  /* Imports //////////////////////////////////////////////////////////////////////////////////////////////////////// */

  import { ref } from 'vue';

  import CreditsPage from '@/components/page/CreditsPage.vue';
  import HomePage from '@/components/page/HomePage.vue';
  import LoadingPage from '@/components/page/LoadingPage.vue';
  import VideosPage from '@/components/page/VideosPage.vue';

  /* Orientation lock /////////////////////////////////////////////////////////////////////////////////////////////// */

  const screenOrientation = screen.orientation;
  if ('lock' in screenOrientation && typeof screenOrientation.lock === 'function') {
    void screenOrientation.lock('portrait').catch(() => {
      // noop
    });
  }

  /* App lifecycle ////////////////////////////////////////////////////////////////////////////////////////////////// */

  enum AppLifecycle {
    HOME,
    VIDEO_PLAYER,
    LOADING,
    CREDITS,
  }

  const appLifecycle = ref<AppLifecycle>(AppLifecycle.HOME);

  function onStartWatching() {
    appLifecycle.value = AppLifecycle.VIDEO_PLAYER;
  }

  function onScrollComplete() {
    appLifecycle.value = AppLifecycle.CREDITS;
  }

  function onReloadVideos() {
    appLifecycle.value = AppLifecycle.LOADING;
    setTimeout(() => {
      appLifecycle.value = AppLifecycle.VIDEO_PLAYER;
    }, 1000);
  }

  function onRestart() {
    appLifecycle.value = AppLifecycle.HOME;
  }
</script>

<template>
  <HomePage
    v-if="appLifecycle === AppLifecycle.HOME"
    @start-watching="onStartWatching"
  />
  <VideosPage
    v-else-if="appLifecycle === AppLifecycle.VIDEO_PLAYER"
    @scroll-complete="onScrollComplete"
    @reload-videos="onReloadVideos"
  />
  <LoadingPage v-else-if="appLifecycle === AppLifecycle.LOADING" />
  <CreditsPage
    v-else-if="appLifecycle === AppLifecycle.CREDITS"
    @restart="onRestart"
  />
</template>
