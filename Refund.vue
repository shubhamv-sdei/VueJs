<template>
  <div class="commission-refund">
    <b-row class="secondrow">
      <b-col
        cols="12"
        sm="12"
        md="12"
        lg="6"
        xl="6"
        class="theme-font-light pl-0"
      >
        <b-row>
          <span class="e theme-font-regular">
            Save thousands with
            <span class="secondarycolor theme-font-semi-bold">Nexme</span>
          </span>
        </b-row>
        <b-row>
          <span class="f" v-html="description || defaultText"></span>
        </b-row>
      </b-col>
      <b-col lg="1" xl="1" class="d-none d-lg-block d-xl-block"></b-col>
      <b-col cols="12" sm="12" md="12" lg="5" xl="5" class="m-pl-0 m-pr-0">
        <b-row>
          <span class="a theme-font-bold">Home price</span>
        </b-row>
        <b-row>
          <img class="loc" src="@/assets/icons/dollar.svg" height="28" />
          <b-input v-model="computedSlider" class="i theme-font-regular" />
        </b-row>
        <b-row>
          <div class="range-input flex-grow-1 my-3">
            <b-form-input
              v-model="slider"
              class="inp-range"
              type="range"
              min="200000"
              max="3000000"
              step="5000"
            ></b-form-input>
            <div
              class="range-output"
              :style="{ width: calc2(slider, 3000000) - 2 + '%' }"
            ></div>
          </div>
        </b-row>
      </b-col>
    </b-row>
    <b-row align-h="center" class="save-refund">
      <span class="b theme-font-regular fw-500">
        You save
        <span class="secondarycolor theme-font-semi-bold"
          >${{ numberWithCommas(((slider * 1.5) / 100).toFixed(0)) }}</span
        >
        {{ text }}
      </span>
    </b-row>
  </div>
</template>
<script>
export default {
  name: "Refund",
  props: {
    text: {
      type: String,
      default: ""
    },
    description: {
      type: String,
      default: ""
    }
  },
  data() {
    return {
      slider: 450000,
      defaultText: `Your Nexme agent partner commits to 50% commission fee, regardless
            of the price. Nexme does not take any commission, we provide all the
            commission refund back to the homebuyer.`
    };
  },
  computed: {
    computedSlider: {
      get() {
        return this.numberWithCommas(this.slider);
      },
      set(val) {
        this.slider = this.numberWithoutCommas(val);
      }
    }
  },
  methods: {
    numberWithCommas(x) {
      return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
    numberWithoutCommas(x) {
      return x.toString().replace(/,/g, "");
    },
    calc2(small, big) {
      const a = small;

      const b = big;

      const c = a / b;

      const d = c * 100;

      return d;
    }
  }
};
</script>
