<template>
  <b-row class="sell-widget">
    <b-row v-show="step === 1" class="step1" no-gutters>
      <b-row no-gutters>
        <b-col cols="6" sm="6" md="4" lg="4" xl="4">
          <FilterBox
            title="House"
            image-path="assets/filter_house.svg"
            image-width="17"
            image-height="15"
            style="min-height:114px;"
          />
        </b-col>
        <b-col cols="6" sm="6" md="4" lg="4" xl="4">
          <FilterBox
            title="Condominium"
            image-path="assets/filter_condo.svg"
            image-width="10"
            image-height="16"
            style="min-height:114px;"
          />
        </b-col>
        <b-col cols="6" sm="6" md="4" lg="4" xl="4">
          <FilterBox
            title="Townhouse"
            image-path="assets/filter_town.svg"
            image-width="18"
            image-height="16"
            style="min-height:114px;"
          />
        </b-col>
        <b-col cols="6" sm="6" md="4" lg="4" xl="4">
          <FilterBox
            title="Multi-family"
            image-path="assets/filter_multi.svg"
            image-width="18"
            image-height="17"
            style="min-height:114px;"
          />
        </b-col>
        <b-col cols="6" sm="6" md="4" lg="4" xl="4">
          <FilterBox
            title="Land"
            image-path="assets/filter_land.svg"
            image-width="17"
            image-height="18"
            style="min-height:114px;"
          />
        </b-col>
        <b-col cols="6" sm="6" md="4" lg="4" xl="4">
          <FilterBox
            title="New construction"
            image-path="assets/filter_new.svg"
            image-width="17"
            image-height="17"
            style="min-height:114px;"
          />
        </b-col>
      </b-row>
    </b-row>
    <b-row v-show="step === 2" class="step2 b theme-font-semi-bold">
      <b-row>
        <b-col cols="12">
          <span>Address</span>
        </b-col>
      </b-row>
      <b-row style="margin-top:2% !important;margin-bottom:2% !important;">
        <b-col cols="12">
          <b-input class="step2input theme-font-light" placeholder="Street" />
        </b-col>
      </b-row>
      <b-row>
        <b-col>
          <b-input class="step2input theme-font-light" placeholder="City" />
        </b-col>
        <b-col>
          <b-input class="step2input theme-font-light" placeholder="State" />
        </b-col>
        <b-col>
          <b-input class="step2input theme-font-light" placeholder="Zip code" />
        </b-col>
      </b-row>

      <b-row
        class="separator"
        style="margin-top:5% !important;margin-bottom:5% !important;"
      ></b-row>
      <b-row>
        <b-col cols="3">
          <span>Bedroom</span>
        </b-col>
        <b-col cols="3">
          <Stepper />
        </b-col>
        <b-col cols="3">
          <span>Bathroom</span>
        </b-col>
        <b-col cols="3">
          <Stepper />
        </b-col>
      </b-row>
      <b-row style="margin-top:5% !important;margin-bottom:5% !important;">
        <b-col>
          <b-row>
            <span>Square feet</span>
          </b-row>
          <b-row>
            <b-input type="number" class="step2input theme-font-light" />
          </b-row>
        </b-col>
        <b-col>
          <b-row>
            <span>HOA fees</span>
          </b-row>
          <b-row>
            <b-input type="number" class="step2input theme-font-light" />
          </b-row>
        </b-col>
        <b-col>
          <b-row>
            <span>Year built</span>
          </b-row>
          <b-row>
            <b-input type="number" class="step2input theme-font-light" />
          </b-row>
        </b-col>
        <b-col>
          <b-row>
            <span>Home use</span>
          </b-row>
          <b-row>
            <b-input class="step2input theme-font-light" />
          </b-row>
        </b-col>
      </b-row>
    </b-row>
    <b-row v-show="step === 3" class="step3">
      <b-row>
        <b-input disabled class="price theme-font-light" :value="slidertext" />
      </b-row>
      <b-row style="margin-top:10% !important;margin-bottom:10% !important;">
        <b-input
          v-model="sellPrice"
          type="range"
          class="slider"
          min="200000"
          max="3000000"
          step="5000"
        />
      </b-row>
      <b-row class="theme-font-light a">
        <span>You save&nbsp;</span>
        <span class="secondarycolor theme-font-regular"
          >${{ numberWithCommas(((3 * sellPrice) / 100).toFixed(0)) }}</span
        >
        <span>&nbsp;selling with Nexme</span>
      </b-row>
    </b-row>
  </b-row>
</template>

<script>
import FilterBox from "./FilterBox";
import Stepper from "./Stepper";

export default {
  name: "SellWidget",
  components: {
    FilterBox,
    Stepper
  },
  data() {
    return {
      step: 1,
      sellPrice: 200000
    };
  },
  computed: {
    slidertext() {
      return "$" + this.numberWithCommas(this.sellPrice);
    }
  },
  methods: {
    numberWithCommas(x) {
      return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
    incrementStep() {
      this.step++;
    },
    decrementStep() {
      this.step--;
    }
  }
};
</script>
