<script setup lang="ts">
  /* Imports //////////////////////////////////////////////////////////////////////////////////////////////////////// */

  import { ref, watch } from 'vue';

  import { videos, type Video } from '@/constants/videos';

  import VideoPlayer from '@/components/views/VideoPlayer.vue';

  /* Emits ////////////////////////////////////////////////////////////////////////////////////////////////////////// */

  const emit = defineEmits<{
    (event: 'scroll-complete'): void;
    (event: 'reload-videos'): void;
  }>();

  /* Shuffle //////////////////////////////////////////////////////////////////////////////////////////////////////// */

  function shuffle(array: Video[]) {
    return array
      .map((value) => ({ value, sort: Math.random() }))
      .sort((a, b) => a.sort - b.sort)
      .map(({ value }) => value);
  }

  /* Queue ////////////////////////////////////////////////////////////////////////////////////////////////////////// */

  const videoContainer = ref<HTMLDivElement>();

  const queue = ref<Video[]>(shuffle(videos));

  const currentVideoIndex = ref(0);

  watch(currentVideoIndex, (newIndex) => {
    const viewPortHeight = videoContainer.value?.clientHeight ?? 0;
    const newScrollPosition = newIndex * viewPortHeight;
    videoContainer.value?.scrollTo({
      top: newScrollPosition,
      behavior: 'smooth',
    });
  });

  function getVideoVariant(index: number) {
    if (index === currentVideoIndex.value) {
      return 'active';
    } else if (Math.abs(index - currentVideoIndex.value) <= 2) {
      return 'preload';
    } else {
      return 'disabled';
    }
  }

  function playPreviousVideo() {
    const nextIndex = currentVideoIndex.value - 1;
    if (nextIndex < 0) {
      emit('reload-videos');
      return;
    }

    currentVideoIndex.value = nextIndex;
  }

  function playNextVideo() {
    const nextIndex = currentVideoIndex.value + 1;
    if (nextIndex >= queue.value.length) {
      emit('scroll-complete');
      return;
    }

    currentVideoIndex.value = nextIndex;
  }

  /* Drag and drop ////////////////////////////////////////////////////////////////////////////////////////////////// */

  enum SwipeDirection {
    NONE,
    UP,
    DOWN,
  }

  const dragThreshold = 50;

  let dragStartY = 0;

  const onTouchStart = (event: TouchEvent) => {
    dragStartY = event.touches.item(0)?.clientY ?? 0;
  };

  const onTouchEnd = (event: TouchEvent) => {
    const endY = event.changedTouches.item(0)?.clientY ?? 0;
    const diff = endY - dragStartY;

    let swipeDirection = SwipeDirection.NONE;
    if (diff < -dragThreshold) {
      swipeDirection = SwipeDirection.UP;
    } else if (diff > dragThreshold) {
      swipeDirection = SwipeDirection.DOWN;
    }

    switch (swipeDirection) {
      case SwipeDirection.UP: {
        playNextVideo();
        break;
      }
      case SwipeDirection.DOWN: {
        playPreviousVideo();
        break;
      }
      case SwipeDirection.NONE: {
        break;
      }
    }
  };
</script>

<template>
  <div class="videos-page">
    <div class="tabs">
      <i class="pi pi-video"></i>
      <div class="tab">Explore</div>
      <div class="tab">Following</div>
      <div class="tab underlined">For You</div>
      <div
        class="tab"
        @click="emit('scroll-complete')"
      >
        Credits
      </div>
      <i class="pi pi-search"></i>
    </div>

    <div
      ref="videoContainer"
      class="video-container"
      @touchstart="onTouchStart"
      @touchend="onTouchEnd"
    >
      <VideoPlayer
        v-for="(video, index) of queue"
        :key="video.id"
        :video="video"
        :variant="getVideoVariant(index)"
      />
    </div>

    <div class="control-bar">
      <div class="control-button selected">
        <i class="pi pi-home"></i>
        <span>Home</span>
      </div>
      <div class="control-button">
        <i class="pi pi-users"></i>
        <span>Friends</span>
      </div>
      <div class="plus-button">
        <i class="pi pi-plus"></i>
      </div>
      <div class="control-button">
        <i class="pi pi-inbox"></i>
        <span>Inbox</span>
      </div>
      <div class="control-button">
        <i class="pi pi-user"></i>
        <span>Profile</span>
      </div>
    </div>
  </div>
</template>

<style scoped>
  .videos-page {
    --control-bar-height: 55px;

    height: 100%;
    width: 100%;
    overflow: hidden;
    position: relative;
  }

  .tabs {
    position: absolute;
    z-index: 1;
    top: 0;
    left: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 12px;
    padding: 20px;
    width: 100%;

    .tab {
      font-weight: 500;
      font-size: 14px;
      letter-spacing: 0.5px;

      &.underlined {
        text-decoration: underline;
        text-underline-offset: 6px;
        text-decoration-thickness: 2px;
      }
    }

    i {
      --p-icon-size: 24px;

      &:first-child {
        margin-right: auto;
      }

      &:last-child {
        margin-left: auto;
      }
    }
  }

  .video-container {
    height: calc(100% - var(--control-bar-height));
    overflow: hidden;

    .slide-container {
      position: relative;
      width: 100%;
      height: 100%;
    }

    .previous-video,
    .next-video {
      position: absolute;
    }

    .previous-video {
      bottom: 100%;
    }

    .next-video {
      top: 100%;
    }
  }

  .control-bar {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 1;
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 10px;
    padding: 0 30px;
    height: var(--control-bar-height);
    width: 100%;
    background-color: #000;

    .control-button {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      gap: 4px;
      color: #bbb;

      &.selected {
        color: #fff;
      }

      i {
        --p-icon-size: 22px;
      }

      span {
        font-size: 12px;
      }
    }

    .plus-button {
      width: 50px;
      height: 30px;
      background-color: #fff;
      border-radius: 10px;
      display: flex;
      justify-content: center;
      align-items: center;
      color: #000;
      border-right: 5px solid rgb(255, 0, 166);
      border-left: 5px solid rgb(0, 191, 255);

      i {
        --p-icon-size: 16px;

        font-weight: bold;
      }
    }
  }
</style>
