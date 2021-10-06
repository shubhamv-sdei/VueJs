<template>
  <div>
    <b-row>
      <span class="heading theme-font-bold">{{ getFormTitle }}</span>
    </b-row>

    <b-form class="userInfoForm" @submit.prevent="submitForm">
      <b-form-row>
        <b-form-group
          id="firm-name"
          label="Firm"
          label-for="firm"
          class="col-md-12 m-mt-0"
        >
          <b-form-input
            id="firm"
            v-model="form.firm"
            type="text"
            required
            placeholder="Enter the name of the firm you work with"
            :class="formErrors.indexOf('firm') !== -1 ? 'is-invalid' : ''"
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('firm') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid firm name.
          </div>
        </b-form-group>
      </b-form-row>

      <b-form-row>
        <b-form-group
          id="firm-address"
          label="Address"
          label-for="address"
          class="col-md-12"
        >
          <b-form-input
            id="address"
            v-model="form.firmAddress"
            type="text"
            required
            placeholder="Enter your firm address"
            :class="
              formErrors.indexOf('firmAddress') !== -1 ? 'is-invalid' : ''
            "
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('firmAddress') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid address.
          </div>
        </b-form-group>
      </b-form-row>

      <b-form-row class="m-t-25">
        <b-form-group id="firm-city" class="col-md-5 col-xs-12">
          <b-form-input
            id="city"
            v-model="form.firmCity"
            type="text"
            required
            placeholder="City"
            :class="formErrors.indexOf('firmCity') !== -1 ? 'is-invalid' : ''"
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('firmCity') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid city.
          </div>
        </b-form-group>

        <b-form-group id="firm-state" class="col-md-4 col-7 no-label">
          <b-form-input
            id="state"
            v-model="form.firmState"
            type="text"
            placeholder="State"
            required
            :class="formErrors.indexOf('firmState') !== -1 ? 'is-invalid' : ''"
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('firmState') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid state.
          </div>
        </b-form-group>

        <b-form-group id="firm-zip" class="col-md-3 col-5 no-label">
          <b-form-input
            id="zip"
            v-model="form.firmZip"
            type="number"
            min="10000"
            max="99999"
            placeholder="Zip"
            required
            pattern="[0-9]{5}"
            title="5 digit zip code"
            :class="formErrors.indexOf('firmZip') !== -1 ? 'is-invalid' : ''"
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('firmZip') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid zipcode.
          </div>
        </b-form-group>
      </b-form-row>

      <b-form-row>
        <b-form-group
          id="firm-bd"
          label="State license number"
          class="col-md-6"
          label-for="license-number"
        >
          <b-form-input
            id="license-number"
            v-model="form.licenseNumber"
            type="text"
            pattern="^[a-zA-Z0-9]{4,20}$"
            placeholder="Enter your real estate license number"
            title="Max 20 character allowed"
            required
            :class="
              formErrors.indexOf('licenseNumber') !== -1 ? 'is-invalid' : ''
            "
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('licenseNumber') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid license number.
          </div>
        </b-form-group>

        <b-form-group
          id="firm-bs"
          label="In business since"
          class="col-md-6"
          label-for="business-since"
        >
          <b-form-input
            id="business-since"
            v-model="form.businessSince"
            v-format-date
            type="text"
            placeholder="Active license since mm/dd/yyyy"
            required
            size="10"
            pattern="(0[1-9]|1[012])[- /.](0[1-9]|[12][0-9]|3[01])[- /.](19|20)\d\d"
            title="E.g 05/24/2017"
            maxlength="10"
            :class="
              formErrors.indexOf('businessSince') !== -1 ? 'is-invalid' : ''
            "
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('businessSince') !== -1"
            class="invalid-feedback"
          >
            Min. of 2 years required
          </div>
        </b-form-group>
      </b-form-row>

      <b-form-row class="m-pt-10">
        <div class="form-group col-1">
          <div class="checkbox">
            <input
              id="BrokerAgreement"
              v-model="form.BrokerAgreement"
              type="checkbox"
              name="BrokerAgreement"
              @click="!form.BrokerAgreement"
            />
            <label class="checkBoxLbl" for="BrokerAgreement"></label>
          </div>
        </div>
        <div class="form-group col-11 pl-3">
          <label class="form-check-label" for="brokerAgreement"
            >By checking this box, you accept the Partner Real Estate
            <a
              href="#"
              class="color-dark text-underline"
              @click="showBrokerAgreement($event)"
              >Broker Agreement</a
            >
          </label>
        </div>
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
            @click="previous()"
          >
            Back
          </button>
        </b-col>

        <!-- <b-col cols="6" class="text-right">
          <button
            class="minsize"
            :class="
              'btn btn-primary theme-font-semi-bold' +
                (form.BrokerAgreement ? ' ' : ' btn-default disabled')
            "
            :disabled="form.BrokerAgreement !== true ? true : false"
            type="submit"
          >
            Next
          </button>
        </b-col> -->
        <b-col cols="6" class="text-right">
          <button
            class="minsize"
            :class="
              'btn btn-primary theme-font-semi-bold' +
                (form.BrokerAgreement ? ' ' : ' btn-default disabled')
            "
            :disabled="form.BrokerAgreement !== true ? true : false"
            type="button"
            @click="onSubmitWithEm"
          >
            Submit
          </button>
        </b-col>
      </b-form-row>
    </b-form>
    <b-modal
      id="full-type-modal"
      ref="BrockerAgreement"
      hide-footer
      size="xl"
      title="Broker Agreement"
    >
      <p class="my-4 font-16" v-html="agreementContent"></p>
    </b-modal>
    <!-- <div v-html="agreementContent.data"></div> -->
  </div>
</template>

<script>
import { mapState, mapGetters, mapMutations } from "vuex";
import { Helper } from "@/plugins/mixins/helper";
import data from "@/data/broker-agreement.json";
// import { VBModal } from "bootstrap-vue";

export default {
  name: "FirmInfo",
  mixins: [Helper],
  props: {
    formTitle: {
      type: String,
      default: ""
    }
  },
  data() {
    return {
      agreementContent: "",
      form: {
        firm: "",
        firmAddress: "",
        firmCity: "",
        firmState: "",
        firmZip: "",
        licenseNumber: "",
        businessSince: "",
        BrokerAgreement: false
      },
      formErrors: []
    };
  },
  computed: {
    ...mapState("modules/agent", [
      "errors",
      "step",
      "nextButton",
      "formData",
      "stepTwoForm"
    ]),
    ...mapGetters("modules/agent", [
      "activelabel",
      "getStepLabels",
      "getFormTitle"
    ])
  },
  created() {
    if (!this.isEmpty(this.stepTwoForm)) this.form = this.stepTwoForm;
  },
  mounted() {
    this.scrollToTop();
  },
  methods: {
    ...mapMutations("modules/agent", ["setStep", "setFormFields"]),
    submitForm() {
      const This = this;
      const errs = [];
      This.formErrors = [];

      Object.keys(This.form).forEach(function(key) {
        if (key === "firm") {
          if (!This.isValidName(This.form[key])) errs.push(key);
        }
        if (key === "firmCity") {
          if (!This.isValidName(This.form[key])) errs.push(key);
        }
        if (key === "firmState") {
          if (!This.isValidName(This.form[key])) errs.push(key);
        }
        if (key === "firmZip") {
          if (!This.isValidZip(This.form[key])) errs.push(key);
        }
        if (key === "licenseNumber") {
          if (!This.isValidNumberTwo(This.form[key])) errs.push(key);
        }
        if (key === "businessSince") {
          if (!This.isValidFirmDate(This.form[key])) errs.push(key);
        }
        if (key === "BrokerAgreement") {
          if (This.form[key] !== true) errs.push(key);
        }
      });

      if (errs.length > 0) {
        This.formErrors = errs;
      } else {
        console.log('///////////',This.form);
        return This.form;
        // this.$emit('finished');
        // This.setFormFields({ stepNumber: "stepTwoForm", fields: This.form });
        // This.setStep(1);
      }
    },
    // submitForm() {
    //   const This = this;
    //   This.formErrors = [];
    //   this.$refs.elementsRef.submit();
    // },
    onSubmitWithEm() {
      this.$emit('finished');
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
        this.setStep(-1);

        this.$emit("onprevious");
        this.formError = "";
      }
    },
    showBrokerAgreement($event) {
      $event.preventDefault();
      this.$refs.BrockerAgreement.show();
      this.agreementContent = data.data;
    },
    scrollToTop() {
      window.scrollTo(0, 0);
    }
  }
};
</script>
