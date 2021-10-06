<template>
  <b-row>
    <b-col md="6" class="mx-auto preview-form">
      <hr />
      <h4 class="font-weight-bold mt-5">Offer preview</h4>
      <b-row>
        <b-col md="6" class="mt-4 p-0">
          <b-row>
            <b-col md="12" class="p-0">
              <span
                class="float-left description graycolor theme-font-semi-bold"
                >Listing price:</span
              >
              <span class="float-right value secondarycolor theme-font-light"
                >${{ numberWithCommas(amount) }}</span
              >
            </b-col>
            <b-col md="12" class="p-0 mt-1">
              <span
                class="float-left description graycolor theme-font-semi-bold"
                >Offer amount:</span
              >
              <span class="float-right value secondarycolor theme-font-light">
                ${{ numberWithCommas(amount) }}
              </span>
            </b-col>
            <b-col md="12" class="p-0 mt-1">
              <span
                class="float-left description graycolor theme-font-semi-bold"
                >Cash down:</span
              >
              <span class="float-right value secondarycolor theme-font-light">
                ${{ numberWithCommas(cashDown) }} &nbsp; ({{
                  numberWithCommas(cashDownPercentage)
                }}%)
              </span>
            </b-col>
            <b-col md="12" class="p-0 mt-1">
              <span
                class="float-left description graycolor theme-font-semi-bold"
                >Financing contingency:</span
              >
              <span class="float-right value secondarycolor theme-font-light">
                {{ financialContingencyDays }}
                <span class="graycolor theme-font-semi-bold">days</span></span
              >
            </b-col>
            <b-col md="12" class="p-0 mt-1">
              <span
                class="float-left description graycolor theme-font-semi-bold"
                >Sale contingency:</span
              >
              <span class="float-right value secondarycolor theme-font-light">
                {{ saleContingencyDays }}
                <span class="graycolor theme-font-semi-bold">days</span>
              </span>
            </b-col>
            <b-col md="12" class="p-0 mt-1">
              <span
                class="float-left description graycolor theme-font-semi-bold"
                >Inspection contingency:</span
              >
              <span class="float-right value secondarycolor theme-font-light"
                >{{ inspectionContingencyDays }}
                <span class="graycolor theme-font-semi-bold">days</span>
              </span>
            </b-col>
            <b-col md="12" class="p-0 mt-1">
              <span
                class="float-left description graycolor theme-font-semi-bold"
                >Offer expiration:</span
              >
              <span class="float-right value secondarycolor theme-font-light"
                >{{ offerExpirationDays }}
                <span class="graycolor theme-font-semi-bold">days</span></span
              >
            </b-col>
            <b-col md="12" class="p-0 mt-1">
              <span
                class="float-left description graycolor theme-font-semi-bold"
                >Time to close:</span
              >
              <span class="float-right value secondarycolor theme-font-light"
                >{{ timeToCloseDays }}
                <span class="graycolor theme-font-semi-bold">days</span></span
              >
            </b-col>
          </b-row>
        </b-col>
      </b-row>
      <b-row>
        <b-col md="6" class="mt-5 p-0">
          <div class="title">
            Upload pre-approval letter
          </div>
          <b-form-file class="mt-2" size="sm"></b-form-file>
        </b-col>
      </b-row>
      <b-row>
        <b-col md="10" class="mt-5 p-0">
          <div class="title">
            Notes
          </div>
          <b-form-textarea
            v-model="notes"
            class="shadow-none mt-2"
            placeholder="Additional notes about the terms of your offer."
            rows="7"
            no-resize
            required
          ></b-form-textarea>
        </b-col>
      </b-row>
      <b-row>
        <b-col md="12" class="p-0 mt-5">
          <p class="caution-note text-center">
            Placing your offer now is not legally binging. A certified agent
            will reach out to you for the final steps of placing your offer.
          </p>
          <hr />
        </b-col>
      </b-row>
    </b-col>
  </b-row>
</template>

<script>
export default {
  name: "Preview",
  computed: {
    offerFormInputs() {
      return this.$store.state.offerFormInputs;
    },
    offerType() {
      return this.$store.state.offerFormInputs.offerType();
    },
    offerStatus() {
      return this.$store.state.offerFormInputs.offerStatus;
    },
    amount() {
      return this.$store.state.offerFormInputs.amount;
    },
    cashDown() {
      return this.$store.state.offerFormInputs.cashDown;
    },
    cashDownPercentage() {
      return this.$store.state.offerFormInputs.cashDownPercentage;
    },

    offerExpirationDays() {
      return this.$store.state.offerFormInputs.offerExpirationDays;
    },
    timeToCloseDays() {
      return this.$store.state.offerFormInputs.timeToCloseDays;
    },

    financialContingencyDays() {
      return this.$store.state.offerFormInputs.financialContingencyDays;
    },
    inspectionContingencyDays() {
      return this.$store.state.offerFormInputs.inspectionContingencyDays;
    },
    saleContingencyDays() {
      return this.$store.state.offerFormInputs.saleContingencyDays;
    },

    preApprovalLetterPath() {
      return this.$store.state.offerFormInputs.preApprovalLetterPath;
    },
    notes: {
      get() {
        return this.$store.state.offerFormInputs.notes;
      },
      set(val) {
        this.offerFormInputs.notes = val;
        this.$store.commit("setOfferFormInputs", this.offerFormInputs);
      }
    }
  },
  methods: {
    numberWithCommas(x) {
      return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    }
  }
};
</script>

<style scoped></style>
