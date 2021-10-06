<template>
  <div class="payment-details">
    <div
      class="payment-wrapper"
      :class="
        formType == 'Refinance'
          ? 'refinance-payment-wrapper'
          : 'buy-payment-wrapper'
      "
    >
      <form method="post" @submit.prevent="$emit('form_submit')">
        <b-row>
          <b-col md="12" class="main-title">
            <h4 class="font-weight-bold">Payment details</h4>
          </b-col>
          <b-col md="12" class="p-0">
            <b-row>
              <b-col cols="12" md="7" lg="6" xl="6" class="mt-4">
                <label class="title">{{
                  formType == "Buy"
                    ? "Purchase price"
                    : "Your estimated home value"
                }}</label>
                <div class="form-input">
                  <span class="prepend-icon">$</span>
                  <b-form-input
                    ref="$purchasePrice"
                    v-model="propertyPrice"
                    name="purchasePrice"
                    type="text"
                    reuired
                    class="input shadow-none"
                    @focusout="propPrice($event)"
                  ></b-form-input>
                </div>
              </b-col>
            </b-row>
          </b-col>
          <b-col md="12" class="p-0">
            <b-row>
              <b-col cols="12" md="7" lg="6" xl="6" class="mt-4">
                <label class="title"
                  >{{
                    formType == "Buy"
                      ? "Cash down"
                      : "Your current loan balance"
                  }}
                  <img
                    v-if="formType == 'Buy'"
                    v-b-popover.hover.top="cashDown"
                    src="@/assets/icons/info.svg"
                    title="What is cash down?"
                    variant="primary"
                /></label>
                <b-row v-if="formType == 'Buy'" class="cash-down-inputs">
                  <b-col md="7" class="col-7 pr-md-0 px-0">
                    <div class="form-input">
                      <span class="prepend-icon">$</span>
                      <b-form-input
                        v-model="propertyCashDown"
                        name="cashDown"
                        class="input no-right-radius shadow-none"
                        type="text"
                        reuired
                        @focusout="cashDownVal($event)"
                      ></b-form-input>
                    </div>
                  </b-col>
                  <b-col md="5" class="col-5 pl-md-0 px-0 percenet-input">
                    <div class="form-input">
                      <span class="prepend-icon">%</span>
                      <b-form-input
                        v-model="rangePercent"
                        name="percent"
                        type="number"
                        min="0"
                        max="100"
                        class="input no-left-radius shadow-none"
                      ></b-form-input>
                    </div>
                  </b-col>
                </b-row>
                <div v-if="formType == 'Buy'" class="row">
                  <b-col md="12" class="mt-0 px-0">
                    <div class="range-input my-3">
                      <input
                        v-model="rangePercent"
                        type="range"
                        min="0"
                        max="100"
                        step="1"
                        class="mt-4 mb-4 border-0 inp-range custom-range"
                        @change="percentageChange($event)"
                      />
                      <div
                        class="range-output"
                        :style="{ width: rangePercent + '%' }"
                      ></div>
                    </div>
                  </b-col>
                </div>
                <div v-if="formType == 'Refinance'" class="form-input">
                  <span class="prepend-icon">$</span>
                  <b-form-input
                    v-model="loanBalance"
                    v-comma-separate
                    name="purchasePrice"
                    type="text"
                    reuired
                    class="input shadow-none"
                    @focusout="updateLoanBalance($event)"
                  ></b-form-input>
                </div>
              </b-col>
            </b-row>
          </b-col>
        </b-row>
        <div class="row">
          <div class="col-md-12 mb-5 mt-4">
            <b-row class="mortgage-footer-buttons">
              <b-col md="12" class="pl-0 pr-0 buttonsCol">
                <hr class="mb-4" />
                <button
                  class="btn btn-primary float-right theme-font-bold minsize"
                  type="submit"
                >
                  Submit
                </button>
                <button
                  type="button"
                  class="btn btn-secondary theme-font-bold minsize"
                  @click="previousTab"
                >
                  Back
                </button>
              </b-col>
            </b-row>
          </div>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: "PaymentDetails",
  data() {
    return {
      propertyPrice: null,
      propertyCashDown: 0,
      rangePercent: 20,
      loanBalance: null,
      currentActive: 0,
      totalTabs: 0,
      formType: "",
      cashDown: "Need information for this tooltip."
    };
  },
  computed: {
    morgtageFormInputs() {
      return this.$store.state.morgtageFormInputs;
    }
  },
  mounted() {
    window.scroll(0, 0);
    this.formType = this.$store.getters.getPropertyType;
  },
  methods: {
    previousTab() {
      this.$store.commit("setMortgageTab", 1);
    },
    propPrice($event) {
      if (this.propertyPrice) {
        const numVal = $event.target.value.replace(/,/g, "");
        if (isNaN(Number(numVal))) {
          this.propertyPrice = null;
        } else {
          this.propertyPrice = this.propertyPrice.replace(/,/g, "");
          this.$store.commit("setFormInputsPrice", this.propertyPrice);
          this.propertyPrice = this.propertyPrice
            .toString()
            .replace(/\B(?=(\d{3})+(?!\d))/g, ",");
          const cashD = (
            this.propertyPrice.replace(/,/g, "") *
            (this.rangePercent / 100)
          ).toFixed(2);
          this.propertyCashDown = cashD
            .toString()
            .replace(/\B(?=(\d{3})+(?!\d))/g, ",");
        }
      }
    },
    cashDownVal($event) {
      if (this.propertyCashDown) {
        const numVal = $event.target.value.replace(/,/g, "");
        if (isNaN(Number(numVal))) {
          this.propertyCashDown = null;
          $event.preventDefault();
        } else if (this.propertyPrice) {
          const cashNum = Number(numVal).toFixed(0);
          this.$store.commit("setCashDown", cashNum);

          const cashDwn = Number(numVal).toFixed(2);
          const cashStr = cashDwn
            .toString()
            .replace(/\B(?=(\d{3})+(?!\d))/g, ",");
          $event.target.value = cashStr;
          this.propertyCashDown = cashStr;

          const ppVal = this.propertyPrice.replace(/,/g, "");
          const ppNum = Number(ppVal).toFixed(0);
          this.$store.commit("setFormInputsPrice", ppNum);
          const rmDecmal = (cashNum * 100) / ppNum;

          this.rangePercent = rmDecmal.toFixed(0);
        }
      }
    },
    percentageChange($event) {
      const val = $event.target.value;
      this.$store.commit("setLoanBalance", val);
      this.rangePercent = val;

      if (this.propertyPrice) {
        const cashD = (
          this.propertyPrice.replace(/,/g, "") *
          (this.rangePercent / 100)
        ).toFixed(2);
        this.propertyCashDown = cashD
          .toString()
          .replace(/\B(?=(\d{3})+(?!\d))/g, ",");
        this.$store.commit("setCashDown", cashD);
      }
    },
    updateLoanBalance($event) {
      const loanBlc = $event.target.value.replace(/,/g, "");
      const loadBlcNum = Number(loanBlc).toFixed(0);
      this.$store.commit("setLoanBalance", loadBlcNum);
    }
  }
};
</script>

<style scoped></style>
