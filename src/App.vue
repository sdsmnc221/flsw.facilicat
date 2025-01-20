<script setup lang="ts">
import WaterEffect from "./components/WaterEffect.vue";
</script>

<template>
  <WaterEffect image-url="/chatpieces_background.jpg"></WaterEffect>

  <div class="shadow-overlay"></div>
  <main>
    <h1 class="ui-heading font-bold leading-none">
      <img src="/border.png" alt="" />
      <span class="color-blue inline-block pl-12 pt-12">
        une campagne, <br />
        qui deviendra <br />
        bientôt <br />
        un service.
      </span>
    </h1>

    <h2 class="ui-subtitle leading-tight text-right text-white">
      <img src="/logo.png" alt="Logo" />
      <p>
        <span class="font-bold bg-sky-600"
          >combinant expertise féline <br />
          et service social,
        </span>
        <span class="bg-sky-700">
          pour vous permettre <br />
          de profiter sereinement de <br />
          la présence apaisante de vos chats.
        </span>
      </p>
    </h2>

    <p class="ui-scan-code flex flex-col justify-center items-center">
      <span class="ui-scan-code__heading text-sky-700 bg-sky-200 font-bold"
        >Enquête de besoin</span
      >
      <a
        href="https://felisweet.fillout.com/facilicat"
        target="_blank"
        title="Enquête de besoin"
        class="link-qr"
      >
        <img src="/qr.png" alt="QR" />
      </a>
      <a
        href="https://felisweet.fillout.com/facilicat"
        target="_blank"
        title="Enquête de besoin"
        class="link-explicit text-white text-xs bg-sky-600"
      >
        <span>felisweet.fillout.com/facilicat</span>
      </a>
    </p>
  </main>
</template>

<style lang="scss" scoped>
body {
  font-family: "Nunito", sans-serif;
  font-weight: 500;
  position: relative;
  overflow-y: hidden;
  width: 100vw;

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

  .ui-heading {
    &::after {
      content: "";
      display: block;
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
      position: absolute;
      z-index: -1;
      transform: rotate(-2deg);
      transform-origin: top left;
    }

    img {
      position: absolute;
      top: 32px;
      left: 32px;
      display: inline-block;
      width: 100px;
      height: auto;
    }
  }

  .ui-subtitle {
    filter: drop-shadow(0 10px 20px rgba(0, 0, 0, 0.24));

    img {
      position: relative;
      left: 100%;
      display: block;
      right: 0;
      top: 32px;
      width: 480px;
      height: auto;
      transform: translateX(calc(-480px - 12vw));
    }

    p {
      margin-right: 20vw;
    }
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

      p {
        margin-right: 10vw;
        margin-top: 20px;
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
