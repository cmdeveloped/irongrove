<template>
  <div class="shutters">
    <div
      v-for="(photo, idx) in $attrs.photos"
      :class="[
        'shutter',
        $attrs.active === idx
          ? 'active'
          : $attrs.active !== null
          ? 'inactive'
          : ''
      ]"
      :key="'shutter-' + idx"
      @click="setActive(idx)"
    >
      <div
        class="shutter__inner"
        :style="{
          backgroundImage: `url( ${photoPath(photo.photo)} )`
        }"
      >
        <button
          type="button"
          name="shutter-back"
          class="primary-btn"
          @click.stop="imageBack()"
          v-if="$parent.active !== null && $parent.active === idx"
        >
          <i class="fas fa-left"></i> Back
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  methods: {
    toTop() {
      const winWidth = window.outerWidth;
      if (winWidth < 1024) {
        window.scrollTo({ top: 0, behavior: "smooth" });
      }
    },
    setActive(idx) {
      this.$parent.active = idx;
      this.toTop();
    },
    imageBack() {
      this.$parent.active = null;
      this.toTop();
    },
    photoPath(info) {
      const modern = this.$root.loadWebP;
      let { category, project, photo } = info;
      let inProject = category !== "architecture" ? true : false;
      let viewport = this.viewport;
      let path;

      if (!inProject) {
        path =
          modern && viewport === "desktop"
            ? `assets/desktop/${category}/${photo}.webp`
            : `assets/${viewport}/${category}/${photo}.jpg`;
        return path;
      }

      path =
        modern && viewport === "desktop"
          ? `assets/desktop/${category}/${project}/${photo}.webp`
          : `assets/${viewport}/${category}/${project}/${photo}.jpg`;
      return path;
    }
  },
  computed: {
    viewport() {
      let winSize = window.outerWidth;
      return winSize > 768 ? "desktop" : "mobile";
    }
  }
};
</script>

<style lang="scss" scoped>
@import "@/assets/scss/variables";

.shutters {
  height: 100%;
  @include flex(center, space-between);

  @media (max-width: 1440px) {
    flex-direction: column;
  }

  @media (max-width: 1024px) {
    flex-direction: row;
  }

  @media (max-width: 650px) {
    flex-direction: column;
  }

  .shutter {
    height: calc(100% - 4rem);
    width: calc(33.33% - 1.5rem);
    overflow: hidden;
    position: relative;
    top: 0;
    transition: all 0.75s ease-in-out;

    @media (max-width: 1440px) {
      height: calc(33.33% - 1rem);
      width: 100%;
    }

    @media (max-width: 1024px) {
      height: 100%;
      width: calc(33.33% - 1rem);
    }

    @media (max-width: 650px) {
      height: calc(33.33% - 1rem);
      width: 100%;
    }

    &__inner {
      height: 100%;
      @include background(cover, center);
      padding: 2rem;
      transition: 0.5s;
      cursor: pointer;
      @include flex(flex-end, flex-start);
    }

    &:not(.active) {
      &:hover {
        .shutter__inner {
          transform: scale(1.05);
        }
      }
    }

    &.active {
      height: 100%;
      width: 100%;
      transition-delay: 0.15s;

      @media (max-width: 1440px) {
        flex-shrink: 0;
      }
    }

    &.inactive {
      opacity: 0;
      width: 0;
      transition-delay: 0.1s;

      & + .inactive {
        transition-delay: 0.1s;
      }
    }
  }
}
</style>
