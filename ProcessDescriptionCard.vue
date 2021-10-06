<template>
  <b-row
    :class="selected ? 'bgcard bgcard-active' : 'bgcard'"
    align-v="center"
    @click="onclicked()"
  >
    <b-col cols="5" sm="3" md="4" lg="4" xl="4" class="pl-0 pr-0">
      <img class="img img-fluid side-images" :src="dataObj.displayImage.src" />
    </b-col>
    <b-col cols="7" sm="9" md="8" lg="8" xl="8">
      <b-row class="title theme-font-regular" v-html="dataObj.title"></b-row>
      <b-row class="subtitle theme-font-regular"
        ><span
          >{{ dataObj.subtitle }}
          <a
            v-if="dataObj.showModel"
            v-b-modal.buy-popup
            href="javascript:void(0);"
            ><u> Learn More</u></a
          ></span
        ></b-row
      >
      <!-- <span
        v-if="dataObj.showModel"
        class="btn-link secondarycolor font-18"
        v-b-modal.buy-popup
        >Learn More</span
      >
      <span v-else class=""></span> -->
    </b-col>
    <!-- Model -->
    <b-modal
      id="buy-popup"
      v-if="dataObj.showModel"
      centered
      hide-footer
      size="md"
    >
      <h4 class="color-dark-green">{{ dataObj.model.title }}</h4>
      <p class="font-15">
        {{ dataObj.model.content }}
      </p>
      <p class="font-15" v-if="dataObj.model.subcontent">
        {{ dataObj.model.subcontent }}
      </p>
    </b-modal>
  </b-row>
</template>

<script>
export default {
  name: "ProcessDescriptionCard",
  props: {
    dataObj: {
      type: Object,
      default: () => {
        return {};
      }
    },
    selected: Boolean
  },
  methods: {
    onclicked() {
      this.$emit("on-div-click");
    }
  }
};
</script>
