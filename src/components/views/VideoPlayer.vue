<script setup lang="ts">
  /* Imports //////////////////////////////////////////////////////////////////////////////////////////////////////// */

  import { faker } from '@faker-js/faker';
  import Avatar from 'primevue/avatar';
  import { computed, ref, watch } from 'vue';

  import { type Video } from '@/constants/videos';

  /* Props ////////////////////////////////////////////////////////////////////////////////////////////////////////// */

  interface VideoPlayerProps {
    video: Video;
    variant: 'active' | 'preload' | 'disabled';
  }

  const props = defineProps<VideoPlayerProps>();

  /* Video ////////////////////////////////////////////////////////////////////////////////////////////////////////// */

  const videoElement = ref<HTMLVideoElement>();

  const source = computed(() => `/videos/video-${props.video.id}.mp4`);

  const showPlayIcon = ref(false);

  function play() {
    if (videoElement.value) {
      videoElement.value.play();
      showPlayIcon.value = false;
    }
  }

  function pause() {
    if (videoElement.value) {
      videoElement.value.pause();
      showPlayIcon.value = true;
    }
  }

  function onVideoClick() {
    if (videoElement.value) {
      if (videoElement.value.paused) {
        play();
      } else {
        pause();
      }
    }
  }

  watch(
    () => props.variant,
    (newVariant, oldVariant) => {
      switch (newVariant) {
        case 'active': {
          play();
          break;
        }
        default: {
          if (oldVariant === 'active') {
            setTimeout(() => {
              pause();
            }, 500);
          }
          break;
        }
      }
    },
    {
      immediate: true,
    },
  );

  /* Details //////////////////////////////////////////////////////////////////////////////////////////////////////// */

  const user = `@${faker.internet.username()}`;
  const userImage = faker.image.personPortrait({ size: 64 });

  const descriptionWordCount = Math.floor(Math.random() * 10) + 1;
  const description = faker.lorem.sentence(descriptionWordCount);

  const hashtagsWordCount = Math.floor(Math.random() * 10) + 1;
  const hashtags = Array.from({ length: hashtagsWordCount }, () => {
    const word1 = faker.hacker.adjective();
    const word2 = faker.company.buzzNoun();
    const tag = `#${word1}${word2}`.toLowerCase().replace(/ /g, '');
    return tag;
  }).join(' ');

  const soundImage = faker.image.url({ width: 50, height: 50 });

  /* Stats ////////////////////////////////////////////////////////////////////////////////////////////////////////// */

  function formatStat(value: number): string {
    if (value < 1000) {
      return value.toString();
    }

    const units = [
      { value: 1000000, symbol: 'M' },
      { value: 1000, symbol: 'K' },
    ];

    for (const unit of units) {
      if (value >= unit.value) {
        const num = value / unit.value;
        const rounded = Number(num.toFixed(1));
        // Remove trailing .0 (e.g., 1.0M → 1M)
        return `${rounded % 1 === 0 ? rounded.toFixed(0) : rounded}${unit.symbol}`;
      }
    }

    return value.toString();
  }

  function makeRandomNumber(max: number) {
    return Math.floor(Math.random() * max);
  }

  const likes = makeRandomNumber(10000000);
  const likesFormatted = formatStat(likes);

  const comments = makeRandomNumber(likes / 100);
  const commentsFormatted = formatStat(comments);

  const bookmarks = makeRandomNumber(likes / 1000);
  const bookmarksFormatted = formatStat(bookmarks);

  const shares = makeRandomNumber(likes / 1000);
  const sharesFormatted = formatStat(shares);
</script>

<template>
  <div class="video-player">
    <video
      ref="videoElement"
      class="video"
      :autoplay="variant === 'active'"
      loop
      :controls="false"
      :muted="variant !== 'active'"
      playsinline
      @click="onVideoClick"
    >
      <source
        v-if="variant !== 'disabled'"
        :src="source"
        type="video/mp4"
      />
    </video>

    <i
      v-if="showPlayIcon && variant === 'active'"
      class="play-icon pi pi-play"
      @click="play"
    ></i>

    <div class="details">
      <div class="user">{{ user }}</div>
      <div class="description">{{ description }}</div>
      <div class="hashtags">{{ hashtags }}</div>
    </div>

    <div class="controls">
      <Avatar
        class="image"
        :image="userImage"
        size="large"
        shape="circle"
      />
      <div class="faux-button">
        <i class="pi pi-heart"></i>
        <span class="stat">{{ likesFormatted }}</span>
      </div>
      <div class="faux-button">
        <i class="pi pi-comment"></i>
        <span class="stat">{{ commentsFormatted }}</span>
      </div>
      <div class="faux-button">
        <i class="pi pi-bookmark"></i>
        <span class="stat">{{ bookmarksFormatted }}</span>
      </div>
      <div class="faux-button">
        <i class="pi pi-send"></i>
        <span class="stat">{{ sharesFormatted }}</span>
      </div>
      <Avatar
        class="image"
        :image="soundImage"
        size="large"
        shape="circle"
      />
    </div>
  </div>
</template>

<style>
  .video-player {
    position: relative;
    width: 100%;
    height: 100%;
  }

  .video {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  .play-icon {
    --p-icon-size: 60px;

    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    opacity: 0.5;
  }

  .details {
    position: absolute;
    bottom: 15px;
    left: 10px;
    right: 80px;

    .user {
      font-weight: 600;
      font-size: 16px;
      margin-bottom: 8px;
    }

    .description {
      font-size: 14px;
      margin-bottom: 4px;
    }

    .hashtags {
      font-size: 14px;
      color: #ddd;
    }

    .description,
    .hashtags {
      overflow: hidden;
      display: -webkit-box;
      line-clamp: 2;
      -webkit-line-clamp: 2;
      -webkit-box-orient: vertical;
      text-overflow: ellipsis;
      word-break: break-all;
    }
  }

  .controls {
    position: absolute;
    bottom: 15px;
    right: 10px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 20px;

    .image {
      background-color: #aaa;
    }

    .faux-button {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      gap: 6px;

      i {
        --p-icon-size: 28px;
      }

      .stat {
        font-size: 14px;
      }
    }
  }
</style>
