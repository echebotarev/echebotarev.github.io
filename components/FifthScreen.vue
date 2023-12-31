<script setup lang="ts">
import { onMounted } from "vue";
import Button from "~/components/common/Button.vue";
import VideoContainer from "~/components/common/VideoContainer.vue";

import gsap from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

import storage from "~/composables/useStorage";

const lang = await storage.getItem("lang");
const content = await queryContent(`/content-${lang}`).findOne();
const { isMobile } = useDevice();

const getRadian = (deg: number) => (deg * Math.PI) / 180;
const getTanDeg = (deg: number) => {
  const rad = getRadian(deg);
  return Math.tan(rad);
};

let count = 0;
const refresh = () => {
  ScrollTrigger.refresh();
};
const runRefresh = () => {
  count += 1;
  if (count < 20) {
    setTimeout(() => {
      refresh();
      runRefresh();
    }, 1000);
  }
};

runRefresh();

const cornerDeg = 3;

onMounted(() => {
  if (isMobile) {
    return false;
  }
  const container = document.querySelector(".videos");
  const topVideos = gsap.utils.toArray(
    document.querySelectorAll(".first .video"),
  );

  topVideos.forEach((video: HTMLElement) => {
    const rect = video.getBoundingClientRect();

    gsap.fromTo(
      video,
      {
        x: window.innerWidth,
        y: (window.innerWidth + rect.x) * getTanDeg(cornerDeg),
        rotation: cornerDeg,
        ease: "power2.out",
      },
      {
        x: 0,
        y: rect.x * getTanDeg(cornerDeg),
        rotation: cornerDeg,
        scrollTrigger: {
          trigger: container,
          scrub: true,
          start: "top 80%",
          end: "top 55%",
          immediateRender: false,
          // markers: true,
        },
      },
    );
  });

  const bottomVideos = gsap.utils.toArray(
    document.querySelectorAll(".second .video"),
  );

  bottomVideos.forEach((video: HTMLElement) => {
    const rect = video.getBoundingClientRect();

    gsap.fromTo(
      video,
      {
        x: -window.innerWidth,
        y: -window.innerWidth * getTanDeg(cornerDeg),
        rotation: cornerDeg,
        ease: "power2.out",
      },
      {
        x: 0,
        y: rect.x * getTanDeg(cornerDeg),
        rotation: cornerDeg,
        scrollTrigger: {
          trigger: container,
          scrub: true,
          start: "top 80%",
          end: "top 55%",
          immediateRender: false,
          // markers: true,
        },
      },
    );
  });
});
onMounted(() => {
  const containerBalls = document.querySelector(".container-five");
  const tl = gsap.timeline().to(".container-five .ball", {
    x: `random(0, ${containerBalls?.clientWidth}, 5)`,
    y: `random(0, ${containerBalls?.clientHeight - 100}, 3)`,
    duration: 10,
    ease: "none",
    repeat: -1,
    repeatRefresh: true,
  });
});
</script>

<template>
  <div :class="`container-five relative ${isMobile ? 'pt-10' : 'pt-[100px]'}`">
    <img
      v-for="b in content.cases[3]?.balls"
      :src="b.path"
      alt=""
      class="absolute ball"
    />
    <nuxt-link :to="{ path: '/', hash: '#contacts' }">
      <Button class="mb-[80px]"> {{ content.content.callToAction }} </Button>
    </nuxt-link>

    <h1
      id="video-production"
      :class="`font-bold text-center mb-[30px] ${
        isMobile ? 'text-[40px]' : 'text-[64px]'
      }`"
    >
      Video Production
    </h1>
    <p
      :class="`text-xl mb-[70px] text-center ${
        isMobile ? 'px-3' : 'px-[120px]'
      }`"
    >
      {{ content.content.fifthScreen.description }}
    </p>

    <h1
      :class="`font-bold text-center mb-[30px] ${
        isMobile ? 'text-[40px]' : 'text-[64px]'
      }`"
    >
      {{ content.content.fifthScreen.title2 }}
    </h1>

    <div v-if="isMobile" class="overflow-x-scroll">
      <div class="flex flex-nowrap gap-3 w-[3350px] px-2">
        <div v-for="(v, index) in content.videos.items" :key="index">
          <VideoContainer
            :src="v.url"
            :poster="v.poster"
            class="video relative h-[150px] aspect-video"
          />
        </div>
      </div>
    </div>
    <div v-else class="videos relative px-3">
      <div class="grid grid-cols-6 gap-1 first mb-2">
        <div
          v-for="(v, index) in content.videos.items.filter(
            (v: any) => v.position === 0,
          )"
          :key="index"
          class="video aspect-video"
        >
          <VideoContainer :src="v.url" :poster="v.poster" />
        </div>
      </div>

      <div class="grid grid-cols-6 gap-1 second">
        <div
          v-for="(v, index) in content.videos.items.filter(
            (v: any) => v.position === 1,
          )"
          :key="index"
          class="video aspect-video"
        >
          <VideoContainer :src="v.url" :poster="v.poster" />
        </div>
      </div>
    </div>

    <div :class="`main-video ${isMobile ? 'mt-[30px]' : 'mt-[100px]'}`">
      <VideoContainer
        :src="content.videos.main.url"
        :controls="content.videos.main.controls"
        :width="isMobile ? '100%' : 560"
        :height="isMobile ? '100%' : 315"
        :class="`${isMobile ? 'w-[90%] aspect-video' : 'w-fit h-fit'} mx-auto`"
      />

      <nuxt-link :to="{ path: '/', hash: '#contacts' }">
        <Button class="mb-[80px] mt-[50px]">
          {{ content.content.callToAction }}
        </Button>
      </nuxt-link>
    </div>
  </div>
</template>

<style scoped>
.video {
  border-radius: 4px;
  overflow: hidden;
}
</style>
