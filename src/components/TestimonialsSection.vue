<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";
import { gsap } from "gsap";

const slider = ref(null);
const track = ref(null);
const paused = ref(false);
let tween;
let media;
let hovered = false;
let focused = false;
let visible = false;

function updatePlayback() {
  tween?.paused(paused.value || hovered || focused || !visible || document.hidden);
}

function setHover(value) {
  hovered = value;
  updatePlayback();
}

function setFocus(event) {
  focused = event.type === "focusin" || slider.value.contains(event.relatedTarget);
  updatePlayback();
}

function togglePlayback() {
  paused.value = !paused.value;
  updatePlayback();
}

onMounted(() => {
  media = gsap.matchMedia();
  media.add("(prefers-reduced-motion: no-preference)", () => {
    const rebuild = () => {
      const progress = tween?.progress() ?? 0;
      tween?.kill();
      gsap.set(track.value, { x: 0 });
      const cards = track.value.children;
      const distance = cards[testimonials.length].offsetLeft - cards[0].offsetLeft;
      tween = gsap.fromTo(
        track.value,
        { x: 0 },
        {
          x: -distance,
          duration: distance / 35,
          ease: "none",
          repeat: -1,
          paused: true,
        }
      );
      tween.progress(progress);
      updatePlayback();
    };
    const resizeObserver = new ResizeObserver(rebuild);
    resizeObserver.observe(slider.value);
    const observer = new IntersectionObserver(([entry]) => {
      visible = entry.isIntersecting;
      updatePlayback();
    });
    observer.observe(slider.value);
    document.addEventListener("visibilitychange", updatePlayback);
    rebuild();
    return () => {
      resizeObserver.disconnect();
      observer.disconnect();
      document.removeEventListener("visibilitychange", updatePlayback);
      tween?.kill();
      tween = null;
      gsap.set(track.value, { clearProps: "transform" });
    };
  });
});

onBeforeUnmount(() => media?.revert());

const testimonials = [
  {
    name: "សុខ ដារ៉ា",
    quoteEn:
      "The team explained each step clearly and helped me prepare my documents with confidence.",
    quoteKm:
      "ក្រុមការងារបានពន្យល់ជំហាននីមួយៗយ៉ាងច្បាស់លាស់ និងជួយខ្ញុំរៀបចំឯកសារដោយទំនុកចិត្ត។",
  },
  {
    name: "ចាន់ សុភា",
    quoteEn: "I appreciated the personal attention and clear answers to my questions.",
    quoteKm:
      "ខ្ញុំពេញចិត្តនឹងការយកចិត្តទុកដាក់ និងការឆ្លើយតបយ៉ាងច្បាស់លាស់ចំពោះសំណួររបស់ខ្ញុំ។",
  },
  {
    name: "លី វិសាល",
    quoteEn:
      "Professional support and careful document preparation made the process easier to understand.",
    quoteKm:
      "ការគាំទ្រប្រកបដោយវិជ្ជាជីវៈ និងការរៀបចំឯកសារយ៉ាងយកចិត្តទុកដាក់ បានធ្វើឱ្យដំណើរការកាន់តែងាយយល់។",
  },
];
</script>

<template>
  <section class="testimonials-section">
    <div class="testimonials-container">
      <header class="testimonials-heading">
        <p class="eyebrow">
          <span class="en">Client testimonials</span>
          <span class="km">មតិយោបល់របស់អតិថិជន</span>
        </p>

        <h2>
          <span class="en">What our clients say</span>
          <span class="km">អ្វីដែលអតិថិជននិយាយអំពីយើង</span>
        </h2>

        <p class="heading-description">
          <span class="en"> Personal experiences with our service and support. </span>
          <span class="km">
            បទពិសោធន៍ផ្ទាល់របស់អតិថិជនជាមួយសេវាកម្ម និងការគាំទ្ររបស់យើង។
          </span>
        </p>
      </header>

      <!-- Sample content: replace with approved client testimonials. -->
      <div
        ref="slider"
        class="testimonials-slider"
        @mouseenter="setHover(true)"
        @mouseleave="setHover(false)"
        @focusin="setFocus"
        @focusout="setFocus"
      >
        <div
          class="testimonials-viewport"
          tabindex="0"
          role="region"
          aria-label="Client testimonials"
        >
          <div ref="track" class="testimonials-grid">
            <figure
              v-for="(testimonial, index) in [...testimonials, ...testimonials]"
              :key="index"
              :aria-hidden="index >= testimonials.length ? 'true' : undefined"
              :class="{ 'testimonial-clone': index >= testimonials.length }"
              class="testimonial-card"
            >
              <svg
                class="quote-icon"
                viewBox="0 0 24 24"
                fill="currentColor"
                aria-hidden="true"
              >
                <path
                  d="M4 4h7v7c0 5-2.5 8-7 9v-3c2.5-.8 3.7-2.7 4-5H4V4Zm10 0h7v7c0 5-2.5 8-7 9v-3c2.5-.8 3.7-2.7 4-5h-4V4Z"
                />
              </svg>

              <blockquote>
                <span class="en">{{ testimonial.quoteEn }}</span>
                <span class="km">{{ testimonial.quoteKm }}</span>
              </blockquote>

              <figcaption class="client-details">
                <span class="client-avatar" aria-hidden="true">
                  <svg viewBox="0 0 24 24" focusable="false">
                    <circle cx="12" cy="8" r="4" />
                    <path d="M4 21v-2a8 8 0 0 1 16 0v2Z" />
                  </svg>
                </span>

                <div>
                  <p class="client-name">{{ testimonial.name }}</p>
                  <p class="client-label">
                    <span class="en">Client</span>
                    <span class="km">អតិថិជន</span>
                  </p>
                </div>
              </figcaption>
            </figure>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.testimonials-section {
  padding: 109px 24px;
  background: #fff;
}

.testimonials-container {
  width: 100%;
  max-width: var(--content-max-width);
  margin-inline: auto;
  display: grid;
  grid-template-columns: 1fr 4.2fr;
  column-gap: 24px;
}

.testimonials-heading {
  grid-column: 1 / -1;
  display: grid;
  grid-template-columns: subgrid;
  margin: 0 0 48px;
  text-align: left;
}

.eyebrow {
  grid-column: 1;
  grid-row: 1 / 3;
  align-self: start;
  margin: 0;
  padding-top: 9px;
  color: #3d4a63;
  font-size: 12px;
  font-weight: 600;
  letter-spacing: 0.14em;
}

:global(body:has(#l-km:checked) .testimonials-heading .eyebrow) {
  font-family: "Moul", "Noto Sans Khmer", serif;
  font-weight: 400;
  letter-spacing: 0.04em;
  font-size: 11px;
}

.testimonials-heading h2 {
  grid-column: 2;
  margin: 0 0 0.3em;
  color: #16233a;
  font-size: clamp(2.6rem, 5vw, 5rem);
  font-weight: 500;
  line-height: 1.1;
}

.heading-description {
  grid-column: 2;
  max-width: 38em;
  margin: 0;
  color: #5f6a7d;
  font-size: 16.5px;
  line-height: 1.85;
}

.testimonials-slider {
  grid-column: 2;
  min-width: 0;
}

.testimonials-viewport {
  overflow: hidden;
}

.testimonials-grid {
  position: relative;
  display: flex;
  gap: 24px;
}

.slider-toggle {
  margin-top: 20px;
  padding: 10px 18px;
  border: 1px solid #b9975b;
  background: #fff;
  color: #071a2e;
  font: inherit;
  cursor: pointer;
}

.slider-toggle:focus-visible,
.testimonials-viewport:focus-visible {
  outline: 2px solid #b9975b;
  outline-offset: 3px;
}

.testimonial-card {
  box-sizing: border-box;
  flex: 0 0 calc((100% - 48px) / 3);
  display: flex;
  flex-direction: column;
  min-width: 0;
  margin: 0;
  padding: 32px;
  border: 1px solid #e4e7eb;
  background: #faf9f6;
}

.quote-icon {
  width: 32px;
  height: 32px;
  margin-bottom: 24px;
  color: #b9975b;
}

.testimonial-card blockquote {
  margin: 0 0 32px;
  color: #344154;
  font-size: 17px;
  line-height: 1.9;
}

.client-details {
  display: flex;
  align-items: center;
  gap: 14px;
  margin-top: auto;
  padding-top: 24px;
  border-top: 1px solid #e4e2dc;
}

.client-avatar {
  display: grid;
  place-items: center;
  flex-shrink: 0;
  width: 52px;
  height: 52px;
  border: 1px solid #b9975b;
  border-radius: 50%;
  background: #071a2e;
  color: #d8bc84;
}

.client-avatar svg {
  display: block;
  width: 28px;
  height: 28px;
  fill: currentColor;
}

.client-name {
  margin: 0;
  color: #071a2e;
  font-size: 16px;
  font-weight: 500;
  line-height: 1.7;
}

.client-label {
  margin: 3px 0 0;
  color: #647080;
  font-size: 13px;
  line-height: 1.6;
}

@media (max-width: 900px) {
  .testimonials-section {
    padding: 64px 20px;
  }

  .testimonials-container,
  .testimonials-heading {
    grid-template-columns: 1fr;
  }

  .testimonials-heading .eyebrow,
  .testimonials-heading h2,
  .heading-description,
  .testimonials-slider {
    grid-column: 1;
    grid-row: auto;
  }

  .testimonials-heading .eyebrow {
    margin-bottom: 20px;
  }

  .testimonial-card {
    flex-basis: calc((100% - 24px) / 2);
  }
}

@media (max-width: 600px) {
  .testimonials-section {
    padding: 48px 20px;
  }

  .testimonials-heading {
    margin-bottom: 32px;
  }

  .testimonial-card {
    flex-basis: 100%;
    padding: 28px 24px;
  }

  .testimonial-card blockquote {
    font-size: 16px;
  }
}

@media (prefers-reduced-motion: reduce) {
  .testimonials-viewport {
    overflow-x: auto;
    scroll-snap-type: x mandatory;
  }

  .testimonial-card {
    scroll-snap-align: start;
  }

  .testimonial-clone,
  .slider-toggle {
    display: none;
  }
}
</style>
