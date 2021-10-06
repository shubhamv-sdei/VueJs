<template>
  <div class="property-img-inner">
    <b-row class="image-preview">
      <div class="image-resize" @click="openLightBox()">
        <img src="@/assets/icons/resize.svg" alt="" />
      </div>
      <img
        :src="images[selectedIndex]"
        class="property-main-img"
        width="100%"
        height="100%"
        :style="'max-height:' + windowHeight + 'px'"
        :class="imageClass"
        @click="openLightBox()"
      />
      <div class="slideToRight" @click="nextImage()">
        <img src="@/assets/icons/slide-right.svg" alt="" />
      </div>
      <div class="slideToLeft" @click="previewImage()">
        <img src="@/assets/icons/slide-left.svg" alt="" />
      </div>
    </b-row>
    <div class="slider-navigation">
      <b-row
        id="imageNavigation"
        ref="sl"
        class="slide-images flex-row flex-nowrap"
        no-gutters
      >
        <hooper
          ref="carousel"
          :settings="carouselSettings"
          @slide="updateCarousel"
        >
          <slide v-for="(i, index) in images" :key="i">
            <img
              :src="i"
              width="100%"
              height="100%"
              :class="index != selectedIndex ? 's inactive' : 's imgActive'"
              @click="imgSelected(index)"
            />
          </slide>

          <hooper-navigation slot="hooper-addons"></hooper-navigation>
        </hooper>
      </b-row>
      <!-- <div class="slide-arrow arrow-left" @click="left()"></div>
      <div class="slide-arrow arrow-right" @click="right()"></div> -->
    </div>
    <div v-if="showLightBox" class="property-lightbox">
      <div class="lightbox-back"></div>
      <div class="lightbox-inner">
        <div class="close-slider" @click="closeLightBox()"></div>
        <img
          :src="images[selectedIndex]"
          :style="'max-height:' + windowHeight + 'px'"
          :class="imageClass"
        />
        <div class="lightbox-nav">
          <div class="nav-left" @click="previewImage()"></div>
          <div class="lightbox-page fw-500">
            {{ selectedIndex + 1 }}/{{ images.length }}
          </div>
          <div class="nav-right" @click="nextImage()"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Hooper, Slide, Navigation as HooperNavigation } from "hooper";
import "hooper/dist/hooper.css";

export default {
  name: "ImageScroller",
  components: {
    Hooper,
    Slide,
    HooperNavigation
  },
  props: {
    images: {
      type: Array,
      default: () => {
        return [];
      }
    }
  },
  data() {
    return {
      windowHeight: 300,
      selectedIndex: 0,
      showLightBox: false,
      leftarrow: { x: 0, y: 0 },
      rightarrow: { x: 0, y: 0 },
      imageClass: "",
      carouselData: 0,
      carouselSettings: {
        itemsToShow: 4,
        itemsToSlide: 4,
        infiniteScroll: true,
        wheelControl: false
      }
    };
  },
  watch: {
    carouselData() {
      this.$refs.carousel.slideTo(this.carouselData);
    }
  },
  mounted() {
    this.getScreenSize();
  },
  methods: {
    imgSelected(index) {
      if (this.selectedIndex !== index) {
        this.imageClass = "imageChanged";
        const $this = this;
        setTimeout(function() {
          $this.imageClass = "";
        }, 500);
      }
      this.selectedIndex = index;
    },
    updateCarousel(payload) {
      this.myCarouselData = payload.currentSlide;
    },
    left() {
      const container = this.$el.querySelector("#imageNavigation");
      container.scrollLeft -= this.$refs.sl.clientWidth;
    },
    right() {
      const container = this.$el.querySelector("#imageNavigation");
      const slide = this.$el.querySelector("#imageNavigation div.col-3");
      container.scrollLeft += this.$refs.sl.clientWidth;
      console.log(this.images.length * slide.clientWidth);
      console.log(slide.offsetWidth);
      console.log(container.scrollLeft);
      console.log(container.scrollX);
      if (container.scrollLeft === container.clientWidth) {
        //
      }
    },
    openLightBox() {
      this.showLightBox = true;
    },
    closeLightBox() {
      this.showLightBox = false;
    },
    nextImage() {
      const $this = this;
      setTimeout(function() {
        const container = $this.$el.querySelector("#imageNavigation");
        const elm = document.getElementsByClassName("imgActive")[0];
        container.scrollLeft = container.scrollLeft + elm.offsetWidth;
      }, 100);

      if (this.selectedIndex < this.images.length - 1) {
        this.selectedIndex += 1;
        this.imageClass = "imageChanged";
      } else if (this.selectedIndex === this.images.length - 1) {
        this.selectedIndex = 0;
        this.imageClass = "imageChanged";
      }

      this.carouselData = this.selectedIndex;
      setTimeout(function() {
        $this.imageClass = "";
      }, 500);
    },
    previewImage() {
      const $this = this;
      setTimeout(function() {
        const container = $this.$el.querySelector("#imageNavigation");
        const elm = document.getElementsByClassName("imgActive")[0];
        container.scrollLeft = container.scrollLeft - elm.offsetWidth;
      }, 100);
      if (this.selectedIndex !== 0) {
        this.selectedIndex -= 1;
        this.imageClass = "imageChanged";
      } else if (this.selectedIndex === 0) {
        this.selectedIndex = this.images.length - 1;
        this.imageClass = "imageChanged";
      }

      this.carouselData = this.selectedIndex;
      setTimeout(function() {
        $this.imageClass = "";
      }, 500);
    },
    getScreenSize() {
      this.windowHeight = window.innerHeight - 40;
    },
    elementInViewport(div) {
      let top = div.offsetTop;
      let left = div.offsetLeft;
      const width = div.offsetWidth;
      const height = div.offsetHeight;

      while (div.offsetParent) {
        div = div.offsetParent;
        top += div.offsetTop;
        left += div.offsetLeft;
      }
      const container = this.$el.querySelector("#imageNavigation");
      console.log(container.clientWidth);
      console.log(window.innerHeight);
      return (
        top >= window.pageYOffset &&
        left >= window.pageXOffset &&
        top + height <= window.pageYOffset + window.innerHeight &&
        left + width <= window.pageXOffset + container.clientWidth
      );
    }
  }
};
</script>
