<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

const hero = ref(null);
const background = ref(null);
let media;

onMounted(() => {
  media = gsap.matchMedia();
  media.add("(prefers-reduced-motion: no-preference)", () => {
    gsap.fromTo(background.value,
      { clipPath: "polygon(0% 0%, 0% 0%, -20% 100%, 0% 100%)", scale: 1.08 },
      {
        clipPath: "polygon(0% 0%, 120% 0%, 100% 100%, 0% 100%)",
        scale: 1,
        duration: 1.8,
        ease: "power2.inOut",
        scrollTrigger: {
          trigger: hero.value,
          start: "top 85%",
          end: "bottom top",
          toggleActions: "restart none restart reset",
        },
      }
    );
  }, hero.value);
});

onBeforeUnmount(() => media?.revert());
</script>

<template>
  <section ref="hero" class="hero" id="home">
    <div ref="background" class="hero-background" aria-hidden="true"></div>
    <div class="in">
      <p class="eyebrow">
        <span class="en">Phnom Penh · Kingdom of Cambodia</span
        ><span class="km">រាជធានីភ្នំពេញ · ព្រះរាជាណាចក្រកម្ពុជា</span>
      </p>
      <h1>
        <span class="en">Documents with certainty.<em>Service with integrity.</em></span
        ><span class="km"
          >ឯកសារដែលមានភាពច្បាស់លាស់<em>សេវាកម្មប្រកបដោយសុចរិតភាព</em></span
        >
      </h1>
      <p class="sub">
        <span class="en"
          >Notarial services, authentication and document verification, delivered with
          confidence for individuals, businesses and institutions in Cambodia.</span
        ><span class="km"
          >សេវាសារការី ការបញ្ជាក់ភាពត្រឹមត្រូវ និងការផ្ទៀងផ្ទាត់ឯកសារ ប្រកបដោយទំនុកចិត្ត
          សម្រាប់បុគ្គល អាជីវកម្ម និងស្ថាប័ននៅកម្ពុជា។</span
        >
      </p>
      <div class="btns">
        <a class="go" href="#contact"
          ><span class="en">Book an Appointment</span
          ><span class="km">ណាត់ជួបជាមួយយើង</span><span>→</span></a
        >
        <a class="more" href="#services"
          ><span class="en">Explore Our Services ↓</span
          ><span class="km">ស្វែងយល់ពីសេវាកម្ម ↓</span></a
        >
      </div>
    </div>
    <div class="side">
      <small>01</small><span class="en">Authenticity, accuracy and trust</span
      ><span class="km">ភាពពិតប្រាកដ ភាពត្រឹមត្រូវ និងទំនុកចិត្ត</span>
    </div>
  </section>
</template>

<style scoped>
.hero {
  position: relative;
  isolation: isolate;
  background-image: none;
}

.hero-background {
  position: absolute;
  inset: 0;
  z-index: -1;
  pointer-events: none;
  background-image: var(--hero-img);
  background-size: cover;
  background-position: center bottom;
  background-repeat: no-repeat;
}

@media (max-width: 900px) {
  .hero-background {
    background-image: linear-gradient(rgb(255 255 255 / 62%), rgb(255 255 255 / 62%)),
      var(--hero-img);
    background-position: center, 78% bottom;
  }
}
</style>
