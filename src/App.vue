<template>
  <div class="app" ref="appEl">
    <WaterEffect image-url="/chatpieces_background.jpg"></WaterEffect>

    <div class="light-overlay"></div>

    <div class="shadow-overlay"></div>

    <img class="border" src="/border.png" alt="" />

    <main ref="mainEl">
      <section class="intro w-screen h-screen">
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
            class="baseline fx-shadow-white text-sky-700 mt-4 text-6xl font-bold text-center leading-[3rem] uppercase inline-block"
          >
            Nous aidons
            <strong
              class="text-4xl leading-[3rem] font-semibold text-rose-700 lowercase"
            >
              les personnes en situation de handicap
              <span class="text-2xl leading-[3rem]" v-if="handicapText.length">
                {{ handicapText }}</span
              >
            </strong>
            <strong
              class="text-2xl leading-[3rem] font-semibold text-rose-700 lowercase"
            >
              <span class="text-4xl leading-[3rem]">
                <span class="text-sky-100">ou</span> ayant des difficultés
                <span class="text-2xl leading-[1rem]" v-if="troubleText.length">
                  {{ troubleText }}</span
                >
              </span>
            </strong>
            <span class="text-5xl">
              à s'occuper pleinement <br />
              de leurs chats.
            </span>
          </span>
        </h2>

        <div class="persona" />
      </section>
      <section
        ref="publicSection"
        class="public w-screen min-h-screen relative"
      >
        <section
          class="w-full h-screen flex flex-col justify-center items-center bg-sky-200"
        >
          <h2
            class="baseline fx-shadow-white text-sky-700 mt-4 text-6xl font-bold text-center leading-[3rem]inline-block"
          >
            Notre Public
          </h2>
          <ul
            class="text-2xl leading-[3rem] font-semibold text-cyan-600 text-center mt-4 w-1/3"
          >
            <li>Personnes en situation de handicap</li>
            <li>Personnes âgées & Femmes enceintes</li>
            <li class="leading-[2rem] mb-2">
              Personnes actives ne pouvant pas administrer les traitements
              pendant leurs horaires de travail
            </li>
            <li class="font-bold text-cyan-700 leading-[2rem]">
              Toute personne se trouvant en difficulté physique provisoire ou
              permanente
            </li>
          </ul>
          <figure class="flex mt-10">
            <img
              v-for="mascot in imascots"
              :key="mascot"
              :src="mascot"
              class="w-48 h-48"
            />
          </figure>
        </section>
        <section class="h-screen bg-[#82c1e1]"><h2>Nos Objectifs</h2></section>
        <section class="h-screen bg-sky-200"><h2>Nos Services</h2></section>
      </section>
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
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

import WaterEffect from "./components/WaterEffect.vue";

const mainEl = useTemplateRef<HTMLElement>("mainEl");
const app = useTemplateRef<HTMLElement>("appEl");
const logo = useTemplateRef<HTMLElement>("logoEl");
const baseline = useTemplateRef<HTMLElement>("baselineEl");
const publicSection = useTemplateRef<HTMLElement>("publicSectionEl");

const imascots = [
  "/chatpieces-permanent.png",
  "/chatpieces-temporary.png",
  "/chatpieces-pregnant.png",
  "/chatpieces-invisible.png",
  "/chatpieces-working.png",
  "/chatpieces-traveling.png",
];

const heading: Ref<HTMLElement | null> = ref(null);
const scrollCount: Ref<number> = ref(0);

const handicapProgress: Ref<number> = ref(0);
const handicapText: ComputedRef<string> = computed(() => {
  if (handicapProgress.value >= 0.69) {
    return "";
  }

  if (handicapProgress.value >= 0 && handicapProgress.value < 0.14) {
    return "(permanent)";
  } else if (handicapProgress.value >= 0.14 && handicapProgress.value < 0.29) {
    return "(permanent, ponctuel)";
  } else if (handicapProgress.value >= 0.29 && handicapProgress.value < 0.36) {
    return "(permanent, ponctuel, enceinte)";
  } else if (handicapProgress.value >= 0.36 && handicapProgress.value < 0.52) {
    return "(permanent, ponctuel, enceinte, invisible)";
  }

  return "";
});

const troubleText: ComputedRef<string> = computed(() => {
  if (handicapProgress.value >= 0.8) {
    return "";
  }

  if (handicapProgress.value >= 0.57 && handicapProgress.value < 0.72) {
    return "(travail)";
  } else if (handicapProgress.value >= 0.72) {
    return "(travail, voyage, autres problèmes)";
  }
  return "";
});

const scrollPersona = () => {
  const personaTl = gsap.timeline({
    paused: true,
    scrollTrigger: {
      trigger: baseline.value,
      start: `top+=${window.innerHeight * 2}px`,
      end: `+=${window.innerHeight * 2}px`,
      scrub: true,
      // markers: true,
      onUpdate: (self) => {
        handicapProgress.value = Number(self.progress.toFixed(2));
        console.log(handicapProgress.value);
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
      "30%"
    )
    .to(
      ".persona",
      {
        backgroundImage: "url('/chatpieces-pregnant.png')",
      },
      "40%"
    )
    .to(
      ".persona",
      {
        backgroundImage: "url('/chatpieces-invisible.png')",
      },
      "50%"
    )
    .to(
      ".persona",
      {
        backgroundImage: "url('/chatpieces-working.png')",
      },
      "70%"
    )
    .to(
      ".persona",
      {
        backgroundImage: "url('/chatpieces-traveling.png')",
      },
      "80%"
    )
    .to(
      ".persona",
      {
        backgroundImage: "url('/chatpieces-permanent.png')",
      },
      "90%"
    );
};

onMounted(() => {
  nextTick(() => {
    const tl = gsap.timeline({
      paused: true,
      scrollTrigger: {
        trigger: mainEl.value,
        start: "top top",
        end: `+=${window.innerHeight}px`,
        pin: true,
        scrub: true,
        markers: true,
      },
    });

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
        top: "80px",
        left: "110px",
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

  h2 {
    position: relative;

    &::after {
      z-index: -1;
      content: "";
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      width: 50%;
      height: 50%;
      background: radial-gradient(
        circle,
        rgba(255, 255, 255, 0.72) 0%,
        rgba(255, 255, 255, 0) 50%
      );
      pointer-events: none;
      border-radius: 100%;
      filter: blur(12px);
      backdrop-filter: blur(12px) brightness(2);
    }
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
    text-shadow: 2px 2px 0 rgba(0, 0, 0, 0.72);
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
      filter: drop-shadow(0 10px 10px rgba(255, 255, 255, 0.48));
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
