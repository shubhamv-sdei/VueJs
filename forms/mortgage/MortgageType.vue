<template>
  <div class="mt-5 mortgage-type-comp">
    <h4 class="mortgage-page-title">Mortgage type</h4>
    <p class="title">What do you want to do?</p>
    <form ref="$mortgageTypeFrm" action="" method="post">
      <b-row class="mortgage-type">
        <b-col cols="6" md="5" lg="5" xl="3" class="p-0">
          <b-row
            class="mortgage-card text-center d-flex align-items-center justify-content-center cursor-pointer"
            :class="
              morgtageFormInputs.lendingType === 'Buy' ? 'selected shadow' : ''
            "
            @click="setSelected('Buy')"
          >
            <img src="@/assets/icons/money.svg" />
            <div class="row justify-content-center">
              <span class="title">Buy</span>
            </div>
          </b-row>
        </b-col>
        <b-col cols="6" md="5" lg="5" xl="3" class="p-0 mt-md-0">
          <b-row
            class="mortgage-card text-center d-flex align-items-center justify-content-center cursor-pointer"
            :class="
              morgtageFormInputs.lendingType === 'Refinance'
                ? 'selected shadow'
                : ''
            "
            @click="setSelected('Refinance')"
          >
            <img src="@/assets/icons/money-selected.svg" />
            <div class="row justify-content-center">
              <span class="title">Refinance</span>
            </div>
          </b-row>
        </b-col>
      </b-row>
      <div class="row">
        <div class="col-md-12 mb-5 pl-0 pr-0">
          <b-row class="mortgage-footer-buttons">
            <b-col md="12" class="pl-0 pr-0">
              <hr />
              <button
                class="btn btn-primary float-right theme-font-bold"
                :class="{
                  'back-disabled': morgtageFormInputs.lendingType == ''
                }"
                :disabled="morgtageFormInputs.lendingType == ''"
                @click.prevent="nextTab"
              >
                Next
              </button>
              <button class="btn btn-secondary theme-font-bold" disabled>
                Back
              </button>
            </b-col>
          </b-row>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  name: "MortgageType",
  computed: {
    morgtageFormInputs() {
      return this.$store.state.morgtageFormInputs;
    }
  },
  created() {},
  mounted() {
    window.scroll(0, 0);
  },
  methods: {
    setSelected(val) {
      // const formData = this.formInputs;
      // formData.lendingType = val;
      // this.$store.commit("setFormInputs", formData);
      this.$store.commit("setPropertyType", val);
    },
    nextTab() {
      this.$store.commit("setMortgageTab", 1);
    }
  }
};
</script>

<style scoped></style>
