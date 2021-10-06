<template>
  <b-row>
    <b-col md="6" class="mx-auto offer-form">
      <hr />
      <h4 class="font-weight-bold mt-5">Offer</h4>
      <b-row>
        <b-col md="12" class="mt-4">
          <div class="title">
            How will you pay?
          </div>
          <div class="switch-buttons mt-3">
            <b-button
              v-model="offerType"
              variant="primary"
              class="mortgage-button switch-button shadow-none"
              :class="{ selected: offerType === 'Mortgage' }"
              @click="setHowPayToMortgage"
              >Mortgage</b-button
            >
            <b-button
              v-model="offerType"
              variant="primary"
              class="cash-button switch-button shadow-none"
              :class="{ selected: offerType === 'AllCash' }"
              @click="setHowPayToAllCash"
              >All Cash</b-button
            >
          </div>
        </b-col>
      </b-row>
      <b-row>
        <b-col md="6">
          <div class="mt-4">
            <b-form-group label-size="lg">
              <label class="title">Offer amount</label>
              <span class="prepend-icon">$</span>
              <span class="form-placeholder">0% above list</span>
              <b-form-input
                v-model="amount"
                :class="formErrors.indexOf('amount') !== -1 ? 'is-invalid' : ''"
                type="number"
                class="shadow-none pl-5"
                style="padding-right: 120px;"
              ></b-form-input>
            </b-form-group>
            <div class="form-description">
              <span>Listing price:</span>
              <span class="price"> $ 1400000</span>
            </div>
          </div>
        </b-col>
        <b-col md="6" class="cash-down">
          <div class="mt-4">
            <b-form-group label-size="lg">
              <label class="title"
                >Cash down <img src="@/assets/icons/info.svg" class="ml-1"
              /></label>
              <b-row>
                <b-col md="8" class="px-0">
                  <span class="prepend-icon">$</span>
                  <b-form-input
                    v-model="cashDown"
                    :class="
                      formErrors.indexOf('cashDown') !== -1 ? 'is-invalid' : ''
                    "
                    class="input no-right-radius shadow-none pl-5"
                    type="number"
                  ></b-form-input>
                </b-col>
                <b-col md="4" class="px-0">
                  <span class="prepend-icon">%</span>
                  <b-form-input
                    v-model="cashDownPercentage"
                    class="input no-left-radius shadow-none pl-5"
                    :class="
                      formErrors.indexOf('cashDownPercentage') !== -1
                        ? 'is-invalid'
                        : ''
                    "
                    type="number"
                  ></b-form-input>
                </b-col>
                <input
                  v-model="cashDownPercentage"
                  :class="
                    formErrors.indexOf('cashDownPercentage') !== -1
                      ? 'is-invalid'
                      : ''
                  "
                  type="range"
                  min="0"
                  max="100"
                  step="1"
                  class="slider mt-4 mb-4 border-0"
                />
              </b-row>
            </b-form-group>
          </div>
        </b-col>
      </b-row>
      <hr />
      <b-row>
        <b-col cols="12" md="6" lg="5" xl="3">
          <div class="mt-3 mb-4">
            <b-form-group>
              <label class="title long-title"
                >Offer expiration
                <img src="@/assets/icons/info.svg" class="ml-1"
              /></label>
              <b-col md="10" class="px-0">
                <span class="form-placeholder">days</span>
                <b-form-input
                  v-model="offerExpirationDays"
                  :class="
                    formErrors.indexOf('offerExpirationDays') !== -1
                      ? 'is-invalid'
                      : ''
                  "
                  type="number"
                  class="shadow-none pr-5"
                ></b-form-input>
              </b-col>
            </b-form-group>
          </div>
        </b-col>
        <b-col cols="12" md="6" lg="5" xl="3">
          <div class="mt-3 mb-4">
            <b-form-group>
              <label class="title long-title"
                >Time to close <img src="@/assets/icons/info.svg" class="ml-1"
              /></label>
              <b-col md="10" class="px-0">
                <span class="form-placeholder">days</span>
                <b-form-input
                  v-model="timeToCloseDays"
                  :class="
                    formErrors.indexOf('timeToCloseDays') !== -1
                      ? 'is-invalid'
                      : ''
                  "
                  type="number"
                  class="shadow-none pr-5"
                ></b-form-input>
              </b-col>
            </b-form-group>
          </div>
        </b-col>
      </b-row>
      <hr />
      <b-row>
        <b-col cols="12" md="6" lg="6" xl="4">
          <div class="mt-3 mb-4">
            <b-form-group>
              <label class="title long-title"
                >Financing contingency
                <img src="@/assets/icons/info.svg" class="ml-1"
              /></label>
              <b-col md="7" class="px-0">
                <span class="form-placeholder">days</span>
                <b-form-input
                  v-model="financialContingencyDays"
                  :class="
                    formErrors.indexOf('financialContingencyDays') !== -1
                      ? 'is-invalid'
                      : ''
                  "
                  type="number"
                  class="shadow-none pr-5"
                ></b-form-input>
              </b-col>
            </b-form-group>
          </div>
        </b-col>
        <b-col cols="12" md="6" lg="6" xl="4">
          <div class="mt-3 mb-4">
            <b-form-group>
              <label class="title long-title"
                >Inspection contingency
                <img src="@/assets/icons/info.svg" class="ml-1"
              /></label>
              <b-col md="7" class="px-0">
                <span class="form-placeholder">days</span>
                <b-form-input
                  v-model="inspectionContingencyDays"
                  :class="
                    formErrors.indexOf('inspectionContingencyDays') !== -1
                      ? 'is-invalid'
                      : ''
                  "
                  type="number"
                  class="shadow-none pr-5"
                ></b-form-input>
              </b-col>
            </b-form-group>
          </div>
        </b-col>
        <b-col cols="12" md="6" lg="6" xl="4">
          <div class="mt-3 mb-4">
            <b-form-group>
              <label class="title long-title"
                >Sale contingency
                <img src="@/assets/icons/info.svg" class="ml-1"
              /></label>
              <b-col md="7" class="px-0">
                <span class="form-placeholder">days</span>
                <b-form-input
                  v-model="saleContingencyDays"
                  :class="
                    formErrors.indexOf('saleContingencyDays') !== -1
                      ? 'is-invalid'
                      : ''
                  "
                  type="number"
                  class="shadow-none pr-5"
                ></b-form-input>
              </b-col>
            </b-form-group>
          </div>
        </b-col>
      </b-row>
      <hr />
    </b-col>
  </b-row>
</template>

<script>
export default {
  name: "OfferForm",
  data() {
    return {
      // cashDownPercentage: 20
    };
  },
  computed: {
    formErrors() {
      return this.$store.state.errors;
    },
    offerFormInputs() {
      return this.$store.state.offerFormInputs;
    },
    offerType: {
      get() {
        return this.$store.state.offerFormInputs.offerType;
      },
      set(val) {
        this.offerFormInputs.offerType = val;
        this.$store.commit("setOfferFormInputs", this.offerFormInputs);
      }
    },
    amount: {
      get() {
        return this.$store.state.offerFormInputs.amount;
      },
      set(val) {
        this.offerFormInputs.amount = val;
        this.$store.commit("setOfferFormInputs", this.offerFormInputs);
        this.offerFormInputs.cashDown =
          this.$store.state.offerFormInputs.amount *
          (this.cashDownPercentage / 100);
        this.$store.commit("setOfferFormInputs", this.offerFormInputs);
      }
    },
    cashDown: {
      get() {
        return (
          this.$store.state.offerFormInputs.amount *
          (this.cashDownPercentage / 100)
        );
      },
      set(val) {
        this.offerFormInputs.cashDown = val;
        this.$store.commit("setOfferFormInputs", this.offerFormInputs);
      }
    },
    offerExpirationDays: {
      get() {
        return this.$store.state.offerFormInputs.offerExpirationDays;
      },
      set(val) {
        this.offerFormInputs.offerExpirationDays = val;
        this.$store.commit("setOfferFormInputs", this.offerFormInputs);
      }
    },
    timeToCloseDays: {
      get() {
        return this.$store.state.offerFormInputs.timeToCloseDays;
      },
      set(val) {
        this.offerFormInputs.timeToCloseDays = val;
        this.$store.commit("setOfferFormInputs", this.offerFormInputs);
      }
    },
    financialContingencyDays: {
      get() {
        return this.$store.state.offerFormInputs.financialContingencyDays;
      },
      set(val) {
        this.offerFormInputs.financialContingencyDays = val;
        this.$store.commit("setOfferFormInputs", this.offerFormInputs);
      }
    },
    inspectionContingencyDays: {
      get() {
        return this.$store.state.offerFormInputs.inspectionContingencyDays;
      },
      set(val) {
        this.offerFormInputs.inspectionContingencyDays = val;
        this.$store.commit("setOfferFormInputs", this.offerFormInputs);
      }
    },
    saleContingencyDays: {
      get() {
        return this.$store.state.offerFormInputs.saleContingencyDays;
      },
      set(val) {
        this.offerFormInputs.saleContingencyDays = val;
        this.$store.commit("setOfferFormInputs", this.offerFormInputs);
      }
    },
    cashDownPercentage: {
      get() {
        return this.$store.state.offerFormInputs.cashDownPercentage;
      },
      set(val) {
        this.offerFormInputs.cashDownPercentage = val;
        this.$store.commit("setOfferFormInputs", this.offerFormInputs);
      }
    }
  },
  methods: {
    setHowPayToAllCash() {
      this.offerType = "AllCash";
    },
    setHowPayToMortgage() {
      this.offerType = "Mortgage";
    }
  }
};
</script>

<style scoped></style>
