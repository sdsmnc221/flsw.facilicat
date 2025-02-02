<template>
  <div class="app" ref="appEl">
    <WaterEffect image-url="/chatpieces_background.jpg"></WaterEffect>

    <div class="light-overlay"></div>

    <div class="shadow-overlay"></div>

    <img class="border" src="/border.png" alt="" />

    <main ref="mainEl">
      <h1 ref="heading" class="ui-heading fixed w-full h-[100vh] font-bold">
        <span
          class="fx-shadow text-sky-100 text-3xl leading-tight inline-block"
        >
          une campagne, <br />
          qui deviendra <br />
          bientôt <br />
          un service.
        </span>
      </h1>

      <h2
        class="ui-subtitle fixed w-full h-[100vh] flex flex-col justify-center items-center"
      >
        <img ref="logoEl" src="/logo.png" alt="Logo" />
        <span
          ref="baselineEl"
          class="baseline fx-shadow-white text-sky-700 text-4xl font-bold text-center leading-[1.4rem] uppercase inline-block"
        >
          Nous aidons
          <strong
            class="text-2xl leading-[1.4rem] font-semibold text-rose-700 lowercase"
          >
            les personnes en situation
            <br />
            <span class="text-6xl leading-[2rem]">de handicap</span>
            <br />
            <span class="text-lg leading-[1rem]" v-if="handicapText.length">
              {{ handicapText }}</span
            >

            <!-- <span class="text-sky-100">ou</span> ayant des difficultés -->
          </strong>
          <span class="text-xl"> à s'occuper pleinement de leurs chats. </span>
        </span>
      </h2>

      <div class="persona" />
    </main>
  </div>
</template>

<script setup lang="ts">
import {
  type Ref,
  ref,
  useTemplateRef,
  type ComputedRef,
  computed,
  onMounted,
  nextTick,
  watch,
} from "vue";
import { useScroll } from "@vueuse/core";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

import WaterEffect from "./components/WaterEffect.vue";

const main = useTemplateRef<HTMLElement>("mainEl");
const app = useTemplateRef<HTMLElement>("appEl");
const logo = useTemplateRef<HTMLElement>("logoEl");
const baseline = useTemplateRef<HTMLElement>("baselineEl");

// const { directions } = useScroll(app, {
//   behavior: "smooth",
// });

const heading: Ref<HTMLElement | null> = ref(null);
const scrollCount: Ref<number> = ref(0);

const handicapProgress: Ref<number> = ref(0);
const handicapText: ComputedRef<string> = computed(() => {
  if (handicapProgress.value >= 0 && handicapProgress.value < 0.4) {
    return "(permanent)";
  } else if (handicapProgress.value >= 0.4 && handicapProgress.value < 0.58) {
    return "(permanent, ponctuel)";
  } else if (handicapProgress.value >= 0.58 && handicapProgress.value < 0.82) {
    return "(permanent, ponctuel, enceinte)";
  } else if (handicapProgress.value >= 0.82) {
    return "(permanent, ponctuel, enceinte, invisible)";
  }

  return "";
});

const scrollPersona = () => {
  const personaTl = gsap.timeline({
    paused: true,
    scrollTrigger: {
      trigger: baseline.value,
      start: `top+=${window.innerHeight * 2}px`,
      end: `+=${window.innerHeight}px`,
      scrub: true,
      // markers: true,
      onUpdate: (self) => {
        handicapProgress.value = self.progress;
      },
    },
  });

  personaTl
    .to(
      ".persona",
      {
        backgroundImage: "url('/chatpieces-permanent.png')",
      },
      "0%"
    )
    .to(
      ".persona",
      {
        backgroundImage: "url('/chatpieces-temporary.png')",
      },
      "48%"
    )
    .to(
      ".persona",
      {
        backgroundImage: "url('/chatpieces-pregnant.png')",
      },
      "72%"
    )
    .to(
      ".persona",
      {
        backgroundImage: "url('/chatpieces-invisible.png')",
      },
      "84%"
    );
};

onMounted(() => {
  nextTick(() => {
    if (heading.value && logo.value) {
      const tl = gsap.timeline({
        paused: true,
        scrollTrigger: {
          trigger: heading.value,
          start: "top top",
          end: `+=${window.innerHeight}px`,
          pin: true,
          scrub: true,
          markers: true,
          onLeave: () => {
            scrollCount.value += 1;
            gsap.set(".ui-heading", {
              translateY: 0,
            });
          },
          onEnterBack: () => {
            scrollCount.value -= 1;
          },
        },
      });

      tl.to(heading.value.querySelector("span"), {
        duration: 2,
        ease: "power2.in",
        x: "-42vw",
        y: "-41.4vh",
        color: "red",
        scale: 0.64,
      })
        .fromTo(
          logo.value,
          {
            opacity: 0,
            y: "12vh",
          },
          {
            duration: 2,
            ease: "power2.in",
            opacity: 1,
            y: "-8vh",
          }
        )
        .fromTo(
          baseline.value,
          {
            opacity: 0,
            y: "12vh",
          },
          {
            duration: 2,
            ease: "power2.in",
            opacity: 1,
            y: "-10vh",
          }
        );

      scrollPersona();
    }
  });
});

watch(
  () => scrollCount.value,
  () => {
    gsap.to([".light-overlay", ".border"], {
      duration: 1.2,
      ease: "power2.in",
      opacity: scrollCount.value < 1 ? 0 : 1,
    });
  }
);
</script>

<style lang="scss">
body {
  margin: 0;
  padding: 0;
  overflow-x: hidden;
  height: 100vh;
}

#app {
  min-height: 600vh; // Double viewport height to allow scrolling
  width: 100%;
  position: relative;
}

main {
  height: 100vh;
  width: 100%;
  position: relative;
}

body {
  font-family: "Nunito", sans-serif;
  font-weight: 500;
  position: relative;

  .light-overlay {
    width: 160px;
    height: 100px;
    background: linear-gradient(
      to right,
      rgba(255, 255, 255, 0.72) 0%,
      rgba(255, 255, 255, 0.48) 50%,
      rgba(255, 255, 255, 0) 100%
    );
    top: 32px;
    left: 32px;
    position: fixed;
    z-index: -1;
    transform: rotate(-2deg);
    transform-origin: top left;

    opacity: 0;
  }

  .shadow-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    background: radial-gradient(
      circle at 50% 50%,
      transparent 0%,
      rgba(0, 0, 0, 0.2) 80%,
      rgba(0, 0, 0, 0.4) 100%
    );
  }

  .border {
    position: fixed;
    top: 32px;
    left: 32px;
    display: inline-block;
    width: 100px;
    height: auto;
    border: none;

    opacity: 0;
  }

  .fx-shadow {
    text-shadow: 2px 2px 0 rgba(0, 0, 0, 0.2);
  }

  .fx-shadow-white {
    text-shadow: 2px 2px 0 rgba(255, 255, 255, 0.4);
  }

  .ui-heading {
    top: 0;
    left: 0;

    span {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
    }
  }

  .ui-subtitle {
    filter: drop-shadow(0 10px 20px rgba(0, 0, 0, 0.24));

    top: 0;
    left: 0;

    img {
      width: 480px;
      height: auto;
    }

    .baseline {
      strong {
        filter: drop-shadow(0 10px 10px rgba(255, 255, 255, 0.48));
        display: block;
      }
    }
  }

  .persona {
    position: fixed;
    top: 50vh;
    right: 12vw;
    width: 200px;
    height: 200px;
    aspect-ratio: 1;
    background-image: url("/chatpieces-permanent.png");
    background-size: contain;
    background-repeat: no-repeat;
    background-position: center;
  }

  .ui-scan-code {
    transform: translateY(10vh);

    &__heading {
      filter: drop-shadow(2px 1px 0px rgb(255, 255, 255));
    }

    .link-qr {
      margin-top: -10px;
    }

    .link-explicit {
      filter: drop-shadow(0 10px 20px rgba(255, 255, 255, 1));
    }

    img {
      filter: drop-shadow(0 10px 20px rgba(255, 255, 255, 1));
      background: radial-gradient(
        circle,
        rgba(255, 255, 255, 0.72) 0%,
        rgba(255, 255, 255, 0) 72%
      );
      width: 160px;
      height: auto;
    }

    .link-explicit {
      margin-top: -24px;
    }
  }

  @media screen and (max-width: 768px) {
    .ui-subtitle {
      img {
        transform: translateX(calc(-80vw));
      }

      .baseline {
        transform: translateX(calc(-72vw)) translateY(12px);
      }

      p {
        margin-right: 10vw;
        font-size: 0.92rem;
        transform: translateX(10vw);
      }
    }

    .ui-scan-code {
      transform: translateY(10vh) translateX(5vw);
    }
  }
}
</style>
