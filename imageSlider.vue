<template>
  <div class="image-slider">
    <div>
      <b-carousel
        id="property-image-slider"
        v-model="slide"
        :interval="0"
        controls
        background="#ababab"
        img-width="1024"
        img-height="480"
        style="text-shadow: 1px 1px 2px #333;"
        @sliding-start="onSlideStart"
        @sliding-end="onSlideEnd"
      >
        <div class="imageCounter">{{ slide + 1 + "/" + images.length }}</div>
        <b-carousel-slide v-for="(i, index) in images" :key="i + index">
          <template v-slot:img>
            <img
              class="d-block img-fluid w-100"
              width="1024"
              height="480"
              :src="i"
              @click="openImgList()"
            />
          </template>
        </b-carousel-slide>
      </b-carousel>
    </div>

    <div v-if="showLightBox" class="property-lightbox">
      <div class="lightbox-back"></div>
      <div class="lightbox-inner">
        <div class="close-slider" @click="closeLightBox()"></div>
        <img
          :src="currentImg"
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
    <div v-if="showImgList" class="mobileImgList">
      <div class="listHeader">
        <img
          src="@/assets/icons/go_back.svg"
          alt="Go Back"
          @click="closeImgList()"
        />
      </div>
      <div class="imgList">
        <div v-for="(i, index) in images" :key="i" class="lst">
          <img
            :src="i"
            width="100%"
            height="100%"
            @click="openLightBox(i, index)"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ImageSlider",
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
      slide: 0,
      windowHeight: 300,
      selectedIndex: 0,
      showLightBox: false,
      sliding: null,
      currentImg: "",
      imageClass: "",
      showImgList: false
    };
  },
  mounted() {
    this.scrollToTop();
  },
  methods: {
    openImgList() {
      this.showImgList = true;
      const body = document.getElementsByTagName("body")[0];
      body.style.overflow = "hidden";
    },
    closeImgList() {
      this.showImgList = false;
      const body = document.getElementsByTagName("body")[0];
      body.style.overflow = "";
    },
    scrollToTop() {
      console.log("run");
    },
    onSlideStart() {
      this.sliding = true;
    },
    onSlideEnd() {
      this.sliding = false;
    },
    openLightBox(img, index) {
      this.slide = index !== 0 ? index : 1;
      this.selectedIndex = index;
      this.currentImg = img;
      this.showLightBox = true;
    },
    closeLightBox() {
      this.showLightBox = false;
    },
    nextImage() {
      if (this.selectedIndex < this.images.length - 1) {
        this.selectedIndex += 1;
        this.slide += 1;
        this.currentImg = this.images[this.selectedIndex];
        this.imageClass = "imageChanged";
      } else if (this.selectedIndex === this.images.length - 1) {
        this.selectedIndex = 0;
        this.slide = 0;
        this.currentImg = this.images[0];
        this.imageClass = "imageChanged";
      }
      const $this = this;
      setTimeout(function() {
        $this.imageClass = "";
      }, 1000);
    },
    previewImage() {
      if (this.selectedIndex !== 0) {
        this.slide -= 1;
        this.selectedIndex -= 1;
        this.currentImg = this.images[this.selectedIndex];
        this.imageClass = "imageChanged";
      } else if (this.selectedIndex === 0) {
        this.slide = this.images.length - 1;
        this.selectedIndex = this.images.length - 1;
        this.currentImg = this.images[this.selectedIndex];
        this.imageClass = "imageChanged";
      }
      const $this = this;
      setTimeout(function() {
        $this.imageClass = "";
      }, 1000);
    },
    getScreenSize() {
      this.windowHeight = window.innerHeight - 40;
    }
  }
};
</script>
