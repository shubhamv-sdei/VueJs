<template>
  <div class="property-details">
    <div class="form-wrap">
      <form method="post" @submit.prevent="submitForm">
        <b-row>
          <b-col md="12">
            <h4 class="font-weight-bold page-title">Property details</h4>
            <p v-if="formType == 'Buy'" class="title mt-5 first-title">
              Are you first time home buyer?
            </p>
          </b-col>
          <b-col v-if="formType == 'Buy'" class="buyerType" md="12">
            <button
              class="mr-3"
              type="button"
              :class="
                'btn btn-default ' +
                  (morgtageFormInputs.firstHomeOwner === true ? 'selected' : '')
              "
              @click="setIsFirstTime(true)"
            >
              Yes
            </button>
            <button
              type="button"
              :class="
                'btn btn-default ' +
                  (morgtageFormInputs.firstHomeOwner === false
                    ? 'selected'
                    : '')
              "
              @click="setIsFirstTime(false)"
            >
              No
            </button>
          </b-col>
          <b-col v-if="formType == 'Refinance'" md="12" class="mt-4">
            <div class="form-row">
              <div class="form-group col-md-7">
                <label class="form-label address-section" for="Street">
                  Address
                </label>
                <input
                  id="Street"
                  v-model="street"
                  type="text"
                  class="form-control"
                  name="street"
                  placeholder="Street"
                  required
                />
              </div>
              <div class="form-group col-md-5">
                <label
                  for="city"
                  class="fix-space city-label"
                  v-html="'&emsp;'"
                ></label>
                <input
                  id="city"
                  v-model="city"
                  type="text"
                  class="form-control"
                  name="city"
                  placeholder="City"
                  required
                />
              </div>
            </div>
            <div class="form-row">
              <div class="col-7 form-group col-md-3">
                <input
                  id="State"
                  v-model="state"
                  type="text"
                  class="form-control"
                  name="state"
                  placeholder="State"
                  required
                />
              </div>
              <div class="col-5 form-group col-md-4">
                <input
                  id="zip-code"
                  v-model="zipCode"
                  type="number"
                  class="form-control"
                  name="zipCode"
                  placeholder="Zip code"
                  required
                  pattern="[0-9]{5}"
                  title="5 digit zip code"
                  min="10000"
                  max="99999"
                />
              </div>
            </div>
          </b-col>
          <b-col v-if="formType == 'Buy'" md="12" class="mt-5 plan-purchace">
            <p class="title">When do you plan to purchase your home?</p>
            <b-col md="5" lg="4" class="col-8 p-0">
              <b-row class="form-select-container">
                <b-form-select
                  v-model="selectedMtF"
                  name="selectInMonths"
                  :options="optionsMtF"
                  class="form-select"
                  :class="selectedMtF ? 'selectedColor' : ''"
                  required
                  @change="monthToFoundChange($event)"
                ></b-form-select>
                <span class="custom-appearance">
                  <img src="@/assets/icons/Down-arrow-small.svg" />
                </span>
              </b-row>
            </b-col>
          </b-col>
          <b-col md="12" class="type-of-home-section">
            <p class="title form-label">What type of home?</p>
            <b-col md="12" class="p-0">
              <b-row>
                <b-col
                  cols="4"
                  md="4"
                  lg="2"
                  xl="2"
                  class="icon-holder bg-white col-xs-4"
                  role="button"
                  :class="{
                    selected: morgtageFormInputs.propertyType === 'Home'
                  }"
                  @click="setWhatType('Home')"
                >
                  <div>
                    <img src="@/assets/icons/types/house_selected.svg" />
                  </div>
                  <div class="mt-3">House</div>
                </b-col>
                <b-col
                  cols="4"
                  md="4"
                  lg="2"
                  xl="2"
                  class="icon-holder bg-white col-xs-4"
                  role="button"
                  :class="{
                    selected: morgtageFormInputs.propertyType === 'Condominium'
                  }"
                  @click="setWhatType('Condominium')"
                >
                  <div>
                    <img src="@/assets/icons/types/condo.svg" />
                  </div>
                  <div class="mt-3">Condominium</div>
                </b-col>
                <b-col
                  cols="4"
                  md="4"
                  lg="2"
                  xl="2"
                  class="icon-holder bg-white col-xs-4"
                  role="button"
                  :class="{
                    selected: morgtageFormInputs.propertyType === 'Townhouse'
                  }"
                  @click="setWhatType('Townhouse')"
                >
                  <div>
                    <img src="@/assets/icons/types/townhouse.svg" />
                  </div>
                  <div class="mt-3">Townhouse</div>
                </b-col>

                <b-col
                  cols="4"
                  md="4"
                  lg="2"
                  xl="2"
                  class="icon-holder bg-white col-xs-4"
                  role="button"
                  :class="{
                    selected: morgtageFormInputs.propertyType === 'Multi-family'
                  }"
                  @click="setWhatType('Multi-family')"
                >
                  <div>
                    <img src="@/assets/icons/types/multifamily.svg" />
                  </div>
                  <div class="mt-3">Multi-family</div>
                </b-col>
                <b-col
                  cols="4"
                  md="4"
                  lg="2"
                  xl="2"
                  class="icon-holder bg-white col-xs-4"
                  role="button"
                  :class="{
                    selected: morgtageFormInputs.propertyType === 'Land'
                  }"
                  @click="setWhatType('Land')"
                >
                  <div>
                    <img src="@/assets/icons/types/land.svg" />
                  </div>
                  <div class="mt-3">Land</div>
                </b-col>
                <b-col
                  cols="4"
                  md="4"
                  lg="2"
                  xl="2"
                  class="icon-holder bg-white col-xs-4"
                  role="button"
                  :class="{
                    selected: morgtageFormInputs.propertyType === 'Manufactured'
                  }"
                  @click="setWhatType('Manufactured')"
                >
                  <div>
                    <img src="@/assets/icons/types/mobile-home.svg" />
                  </div>
                  <div class="mt-3 text-wrap">Manufactured</div>
                </b-col>
              </b-row>
              <div v-if="propertyError" class="row">
                <div class="col-md-12 pl-0">
                  <span class="font-13 color-red">
                    Please select your home type.
                  </span>
                </div>
              </div>
            </b-col>
          </b-col>
          <b-col md="12">
            <p class="title form-label section3">
              How are you using this home?
            </p>
            <b-col md="5" lg="4" class="p-0 col-8">
              <b-row class="row form-select-container">
                <b-form-select
                  v-model="propertyUsageSl"
                  name="selectInMonths"
                  :options="propertyUsageOpt"
                  class="form-select"
                  required
                  :class="propertyUsageSl ? 'selectedColor' : ''"
                  @change="propertyTypeUsage($event)"
                ></b-form-select>
                <span class="custom-appearance">
                  <img src="@/assets/icons/Down-arrow-small.svg" />
                </span>
              </b-row>
            </b-col>
          </b-col>
        </b-row>
        <div class="row pt-3">
          <div class="col-md-12 mb-5">
            <b-row class="mortgage-footer-buttons">
              <b-col md="12" class="pl-0 pr-0 pb-4">
                <hr class="footer-seperator" />
                <button
                  type="submit"
                  class="btn btn-primary float-right theme-font-bold minsize"
                  :class="{
                    'back-disabled': morgtageFormInputs.lendingType == ''
                  }"
                  :disabled="optionsMtF == ''"
                >
                  Next
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
  name: "PropertyDetails",
  data() {
    return {
      street: "",
      city: "",
      state: "",
      zipCode: null,
      propertyError: false,
      formType: this.$store.getters.getPropertyType,
      selectedMtF: null,
      optionsMtF: [
        { value: null, text: "Select in months" },
        { value: 1, text: "1 Month" },
        { value: 2, text: "2 Month" },
        { value: 3, text: "3 Month" },
        { value: 4, text: "4 Month" },
        { value: 5, text: "5 Month" },
        { value: 6, text: "6 Month" },
        { value: 7, text: "7 Month" },
        { value: 8, text: "8 Month" },
        { value: 9, text: "9 Month" },
        { value: 10, text: "10 Month" },
        { value: 11, text: "11 Month" },
        { value: 12, text: "12 Month" }
      ],
      propertyUsageSl: null,
      propertyUsageOpt: [
        { value: null, text: "Property type" },
        { value: "primary-residence", text: "Primary residence" },
        { value: "secondary/vacation", text: "Secondary/Vacation" },
        { value: "investment-property", text: "Investment property" }
      ]
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
    monthToFoundChange($event) {
      console.log($event);
      const val = $event;
      this.$store.commit("setMonthsToFund", val);
    },
    propertyTypeUsage($event) {
      const val = $event;
      this.$store.commit("setPropertyTypeUsage", val);
    },
    setIsFirstTime(val) {
      this.$store.commit("setFormInputsOwnerType", val);
    },
    setWhatType(val) {
      this.$store.commit("setFormInputsPropType", val);
    },
    submitForm() {
      if (this.morgtageFormInputs.propertyType === "") {
        this.propertyError = true;
      } else {
        this.propertyError = false;
        let addr = "";
        if (this.formType === "Refinance") {
          addr = `${this.street}, ${this.city} ${this.state}, ${this.zipCode}`;
        } else {
          addr = "No address provided";
        }

        this.$store.commit("setpropertyAddr", addr);
        this.$store.commit("setMortgageTab", 2);
      }
    },
    previousTab() {
      this.$store.commit("setMortgageTab", 0);
    }
  }
};
</script>
