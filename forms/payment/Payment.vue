<template>
  <div>
    <b-row>
      <span class="heading theme-font-bold">{{ getFormTitle }}</span>
    </b-row>

    <b-form class="userInfoForm" @submit.prevent="submitForm">
      <div class="form-row stripe_payment_form">
        <div class="form-group col-md-12">
          <stripe-elements
            ref="elementsRef"
            :pk="publishableKey"
            :email="stepOneForm.email"
            required
            @token="tokenCreated"
            @loading="loading = $event"
          >
          </stripe-elements>
          <!-- stepOneForm.email -->
        </div>

        <b-btn
          v-if="false"
          color="#42b883"
          dark
          @click="$refs.elementsRef.submit()"
          >Add payment Method</b-btn
        >

        <div v-if="form.paymentMethod" class="form-row col-md-12">
          <div class="added-payment-method">
            <img
              :src="
                '/assets/icons/cards/' + cardIcon(form.paymentBrand) + '.svg'
              "
              class="card-icon"
              alt="visa"
            />
            {{ form.paymentBrand }} card ending {{ form.last4 }} added.
          </div>
        </div>
      </div>

      <div v-if="loadingIcon" class="form-row">
        <div
          class="form-group col-md-12 d-flex justify-content-center align-items-center"
        >
          <div class="loading">
            <div class="loading__square"></div>
            <div class="loading__square"></div>
            <div class="loading__square"></div>
            <div class="loading__square"></div>
            <div class="loading__square"></div>
            <div class="loading__square"></div>
            <div class="loading__square"></div>
          </div>
        </div>
      </div>

      <b-form-row v-if="false">
        <b-form-group
          id="card-name"
          label="Name on card"
          label-for="Name on card"
          class="col-md-12"
        >
          <b-form-input
            id="card"
            v-model="form.cardName"
            type="text"
            required
            placeholder="Enter the name on your card"
            :class="formErrors.indexOf('cardName') !== -1 ? 'is-invalid' : ''"
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('cardName') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid name.
          </div>
        </b-form-group>
      </b-form-row>

      <b-form-row v-if="false">
        <b-form-group
          id="card-number"
          label="Card number"
          label-for="cardNumber"
          class="col-md-5"
        >
          <cleave
            id="cardNumber"
            v-model="form.cardNumber"
            required
            class="form-control"
            placeholder="XXXX XXXX XXXX XXXX"
            :options="{ creditCard: true }"
            :class="formErrors.indexOf('cardNumber') !== -1 ? 'is-invalid' : ''"
          ></cleave>
          <div
            v-if="formErrors.indexOf('cardNumber') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid card number. it must be 16 digits.
          </div>
        </b-form-group>

        <b-form-group
          id="exp-date"
          label="Exp. date"
          label-for="expDate"
          class="col-md-4"
        >
          <b-form-input
            id="expDate"
            v-model="form.expDate"
            type="month"
            placeholder="09 / 24"
            required
            :class="formErrors.indexOf('expDate') !== -1 ? 'is-invalid' : ''"
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('expDate') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid expiration date.
          </div>
        </b-form-group>

        <b-form-group
          id="decurity-code-lbl"
          label="Security code"
          label-for="securityCode"
          class="col-md-3"
        >
          <b-form-input
            id="securityCode"
            v-model="form.securityCode"
            type="number"
            min="001"
            max="999"
            pattern="[0-9]{3}"
            placeholder="E.g 188"
            required
            :class="
              formErrors.indexOf('securityCode') !== -1 ? 'is-invalid' : ''
            "
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('securityCode') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid security code. it's 3 digits.
          </div>
        </b-form-group>
      </b-form-row>

      <b-form-row v-if="false">
        <b-form-group
          id="firm-address"
          label="Billing address"
          label-for="address"
          class="col-md-12"
        >
          <b-form-input
            id="address"
            v-model="form.cardAddress"
            type="text"
            required
            :class="
              formErrors.indexOf('cardAddress') !== -1 ? 'is-invalid' : ''
            "
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('cardAddress') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid address.
          </div>
        </b-form-group>
      </b-form-row>

      <b-form-row v-if="false">
        <b-form-group
          id="firm-city"
          label="City"
          label-for="city"
          class="col-md-5"
        >
          <b-form-input
            id="city"
            v-model="form.cardCity"
            type="text"
            required
            :class="formErrors.indexOf('cardCity') !== -1 ? 'is-invalid' : ''"
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('cardCity') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid city.
          </div>
        </b-form-group>

        <b-form-group
          id="firm-state"
          label="State"
          label-for="State"
          class="col-md-4"
        >
          <b-form-input
            id="State"
            v-model="form.cardState"
            v-text_only
            type="text"
            required
            :class="formErrors.indexOf('cardState') !== -1 ? 'is-invalid' : ''"
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('cardState') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid state.
          </div>
        </b-form-group>

        <b-form-group
          id="firm-zip"
          label="Zip"
          label-for="zip"
          class="col-md-3"
        >
          <b-form-input
            id="zip"
            v-model="form.cardZip"
            type="number"
            min="10000"
            max="99999"
            required
            pattern="[0-9]{5}"
            :class="formErrors.indexOf('cardZip') !== -1 ? 'is-invalid' : ''"
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('cardZip') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid zipcode.
          </div>
        </b-form-group>
      </b-form-row>

      <b-form-row class="btn-mft">
        <b-col cols="12" class="text-left">
          <span class="f-s-15"
            >Payment will begin once you complete your first request. Your
            credit card will be charged
            <b class="color-dark-green">$140.00</b> a month. You can cancel at
            any time.</span
          >
        </b-col>
      </b-form-row>

      <div class="separator"></div>
      <b-form-row>
        <b-col cols="6" class="text-left">
          <button
            class="minsize"
            :class="
              'btn theme-font-semi-bold' +
                (step === 1 ? ' btn-default disabled' : ' btn-secondary')
            "
            type="button"
            @click="previous()"
          >
            Back
          </button>
        </b-col>
        <b-col cols="6" class="text-right">
          <button
            class="btn btn-primary theme-font-semi-bold minsize"
            :disabled="loading"
            type="submit"
          >
            Submit
          </button>
        </b-col>
      </b-form-row>
    </b-form>
    <b-modal ref="$signUpError" centered hide-footer>
      <template v-slot:modal-title>
        Error
      </template>
      <div class="d-block text-center">
        <p class="font-17 fw-500 pt-4 pb-3 color-dark">
          {{ checkAgnetSignupError }}
        </p>
      </div>
      <b-button class="mt-3" block @click="goBack()">Try Again</b-button>
    </b-modal>
  </div>
</template>

<script>
import { mapState, mapGetters, mapMutations } from "vuex";
import Cleave from "vue-cleave-component";
import { Helper } from "@/plugins/mixins/helper";
import { BecomeAnAgent } from "@/plugins/services/BecomeAnAgent";
import StripeElements from "@/pages/agent/become-an-agent/checkout/Elements";

export default {
  name: "Payment",
  components: {
    Cleave,
    "stripe-elements": StripeElements
  },
  mixins: [Helper, BecomeAnAgent],
  props: {
    formTitle: {
      type: String,
      default: ""
    }
  },

  data() {
    return {
      form: {
        // cardName: "",
        // cardNumber: "",
        // expDate: "",
        // securityCode: "",
        // cardAddress: "",
        // cardCity: "",
        // cardState: "",
        // pk_test_YCOmd343WtO0VSonC0TtIcO7
        paymentMethod: "",
        last4: 0,
        paymentBrand: ""
      },
      formErrors: [],
      loading: false,
      loadingIcon: false,
      publishableKey: "pk_live_sPg7ftamsCRaIMnicN69NTdQ", // pk_live_sPg7ftamsCRaIMnicN69NTdQ
      token: null,
      charge: null,
      dialog: false,
      amount: 13999
    };
  },
  computed: {
    ...mapState("modules/agent", [
      "errors",
      "step",
      "nextButton",
      "formData",
      "stepTwoForm",
      "stepOneForm"
    ]),
    ...mapGetters("modules/agent", [
      "activelabel",
      "getStepLabels",
      "getFormTitle"
    ]),
    checkAgnetSignupError() {
      return this.$store.getters.getAgentSignupError;
    },
    getPopUpErrorStatus() {
      return this.$store.getters.getPopUpErrprStatus;
    }
  },
  watch: {
    checkAgnetSignupError(err) {
      if (err !== "") {
        // this.$refs.$signUpError.show();
      }
    },
    getPopUpErrorStatus(d) {
      if (d) {
        this.$refs.$signUpError.show();
      }
      this.loadingIcon = d;
      // return this.$store.getters.getPopUpErrprStatus;
    }
  },
  created() {
    if (!this.isEmpty(this.stepThreeForm)) this.form = this.stepThreeForm;
  },
  mounted() {
    this.$root.$on("bv::modal::hide", (bvEvent, modalId) => {
      this.loadingIcon = false;
    });
    this.scrollToTop();
  },
  methods: {
    ...mapMutations("modules/agent", ["setStep", "setFormFields"]),
    submitForm() {
      const This = this;
      This.formErrors = [];
      this.$refs.elementsRef.submit();
    },
    goBack() {
      this.$refs.$signUpError.hide();
      this.loadingIcon = false;
      this.$store.commit("setPopUpErrprStatus", false);
      // this.$router.push({ name: "agent-become-an-agent" });
    },
    onReset(evt) {
      evt.preventDefault();
      this.firm = "";
      this.address = "";
      this.city = "";
      this.state = "";
      this.zip = "";
      this.licenseNnumber = "";
      this.businessSince = "";
      this.BrokerAgreement = "";
    },
    previous() {
      if (this.step > 1) {
        this.setFormFields({ stepNumber: "stepThreeForm", fields: this.form });
        this.setStep(-1);

        this.$emit("onprevious");
        this.formError = "";
      }
    },
    tokenCreated(token) {
      this.token = token;
      if (token) {
        this.charge = {
          payment_method: token.id,
          email: this.stepOneForm.email,
          card: token.card
        };
        this.sendTokenToServer(this.charge);
      } else {
        this.$refs.$signUpError.show();
        this.$store.commit(
          "setAgentSignupError",
          "Sorry, your credit card was declined. Please use a different credit car or make sure you have provide correct information."
        );
      }
    },
    sendTokenToServer(charge) {
      this.loadingIcon = true;
      const $this = this;
      this.form.paymentMethod = charge.payment_method;
      this.form.last4 = charge.card.last4;
      this.form.paymentBrand = charge.card.brand;
      if (this.paymentMethod) {
        $this.formErrors.push("paymentMethod");
      } else {
        const newItem = Object.assign(
          {},
          {
            paymentInfo: $this.form,
            personalInfo: $this.stepOneForm,
            firmInfo: $this.stepTwoForm
          }
        );

        // $this.setStep(1);
        $this.SignUpAgent(newItem);
      }
    },
    cardIcon(val) {
      const cards = [
        "american-express",
        "citi",
        "diners-club",
        "discover",
        "hsbc",
        "jcb",
        "mastercard",
        "visa",
        "unionpay"
      ];
      const name = val.replace(/\s+/g, "-").toLowerCase();
      if (cards.includes(name)) {
        return name;
      } else {
        return "credit-card";
      }
    },
    scrollToTop() {
      window.scrollTo(0, 0);
    }
  }
};
</script>
