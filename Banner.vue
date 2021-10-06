<template>
  <div class="banner">
    <div class="container">
      <div class="row">
        <div class="col-xs-12 col-sm-12 col-md-12 col-lg-7 col-xl-5 pl-0 pr-0">
          <div class="banner-inner">
            <h1 class="theme-font-bold main-color title mob-width">
              {{ heading }}
            </h1>
            <h4 class="theme-font-semi-bold main-color sub-title">
              {{ title }}
            </h4>
            <b-row v-if="inputBox.showInput">
              <CustomTextInput
                class="ci"
                :show-location-icon="inputBox.showLocationIcon"
                :show-search="inputBox.showSearch"
                :placeholder-text="inputBox.placeholderText"
                :show-country="inputBox.showCountry"
                :submit-text="inputBox.submitText"
                :input-type="inputBox.inputType"
                @on-submit="submit"
              />
            </b-row>
            <b-row v-if="inputBox.showSubTitle">
              <p
                class="sub theme-font-regular main-color"
                v-html="subtitle"
              ></p>
            </b-row>
          </div>
        </div>
      </div>
    </div>
    <!-- <div class="banner-inner mx-auto">
      <b-row>
        <h1 class="theme-font-bold main-color title mob-width">
          {{ heading }}
        </h1>
      </b-row>
      <b-row>
        <h4 class="theme-font-semi-bold main-color sub-title">
          {{ title }}
        </h4>
      </b-row>
      <b-row v-if="inputBox.showInput">
        <CustomTextInput
          class="ci"
          :show-location-icon="inputBox.showLocationIcon"
          :show-search="inputBox.showSearch"
          :placeholder-text="inputBox.placeholderText"
          :show-country="inputBox.showCountry"
          :submit-text="inputBox.submitText"
          :input-type="inputBox.inputType"
          @on-submit="submit"
        />
      </b-row>
      <b-row v-if="inputBox.showSubTitle">
        <p class="sub theme-font-regular main-color">{{ subtitle }}</p>
      </b-row>
    </div> -->
  </div>
</template>

<script>
import CustomTextInput from "./CustomTextInput";
export default {
  name: "Banner",
  components: {
    CustomTextInput
  },
  props: {
    heading: {
      type: String,
      default: ""
    },
    title: {
      type: String,
      default: ""
    },
    subtitle: {
      type: String,
      default: ""
    },
    inputBox: {
      type: Object,
      default() {
        return {
          placeholderText: "",
          showLocationIcon: false,
          showCountry: false,
          submitText: "",
          showSearch: false,
          inputType: "",
          showInput: false,
          showSubTitle: false
        };
      }
    }
  },
  data() {
    return {};
  },
  methods: {
    submit(text) {
      this.$store.commit("setSearchInput", text);
      this.$emit("on-submit", text);
    }
  }
};
</script>
