<template>
  <b-container>
    <div class="form-container">
      <b-row>
        <b-col cols="12">
          <b-row>
            <div class="info-steps" no-gutters>
              <ol class="connector">
                <li class="cli">
                  <div class="step-point" :class="step === 1 ? 'active' : ''">
                    <span :class="step === 1 ? 'active' : 'not'">{{
                      steplabels[0]
                    }}</span>
                  </div>
                </li>
                <li class="cli">
                  <div class="step-point" :class="step === 2 ? 'active' : ''">
                    <span :class="step === 2 ? 'active' : 'not'">{{
                      steplabels[1]
                    }}</span>
                  </div>
                </li>
                <!-- <li class="cli">
                  <div class="step-point" :class="step === 3 ? 'active' : ''">
                    <span :class="step === 3 ? 'active' : 'not'">{{
                      steplabels[2]
                    }}</span>
                  </div>
                </li> -->
              </ol>
            </div>
          </b-row>
          <!-- <div class="separator"></div> -->
          <b-row>
            <div v-if="step == 1" class="f-g-1">
              <PersonalInfo ref="PersonalInfo"  @finished="next" />
            </div>
            <div v-if="step == 2" class="f-g-1">
              <FirmInfo ref="FirmInfo" @finished="next" :form-title="formTitle[step - 1]" />
            </div>
            <!-- <div v-if="step == 3" class="f-g-1">
              <Payment ref="Payment" :form-title="formTitle[step - 1]" />
            </div> -->
            <component
              :is="customEl.is"
              v-if="customEl"
              v-model="customEl.props"
              class="el"
            />
          </b-row>

          <!--<b-row v-if="formError">-->
          <!--&lt;!&ndash;<b-alert show variant="warning">{{formError}}</b-alert>&ndash;&gt;-->
          <!--<b-alert show variant="warning">-->
          <!--<ul>-->
          <!--<li v-for="error in errors">{{error}}</li>-->
          <!--</ul>-->
          <!--</b-alert>-->
          <!--</b-row>-->
          <!--<div class="separator"></div>-->
          <!--<b-row>-->
          <!--<b-col cols="2">-->
          <!--<button-->
          <!--:class="-->
          <!--'btn theme-font-semi-bold' + (step === 1 ? ' btn-default disabled' : ' btn-secondary' )"-->
          <!--@click="previous()"-->
          <!--&gt;Back-->
          <!--</button>-->
          <!--</b-col>-->
          <!--<b-col cols="8"></b-col>-->
          <!--<b-col cols="2">-->
          <!--<button-->
          <!--class="btn btn-primary theme-font-semi-bold"-->
          <!--@click="next()"-->
          <!--&gt;{{ (step == maxSteps ? 'Submit' : 'Next') }}-->
          <!--</button>-->
          <!--</b-col>-->
          <!--</b-row>-->
        </b-col>
        <b-col cols="2"></b-col>
      </b-row>
    </div>
  </b-container>
</template>

<script>
import { mapState, mapGetters } from "vuex";
import PersonalInfo from "./forms/personal-info/PersonalInfo";
import Payment from "./forms/payment/Payment";
import FirmInfo from "./forms/firm-info/FirmInfo";
import { Helper } from "@/plugins/mixins/helper";
import { BecomeAnAgent } from "@/plugins/services/BecomeAnAgent";

export default {
  name: "Widget",
  components: {
    PersonalInfo,
    Payment,
    FirmInfo
  },
  mixins: [Helper, BecomeAnAgent],
  props: {
    steplabelgap: {
      type: Number,
      default: 100
    },
    customEl: {
      type: Object,
      default: () => {
        return {};
      }
    }
  },
  data() {
    return {
      steplabels: []
    };
  },
  computed: {
    ...mapState("modules/agent", [
      "errors",
      "step",
      "formError",
      "nextButton",
      "formData",
      "formTitle",
      "maxSteps"
    ]),
    ...mapGetters("modules/agent", ["activelabel", "getStepLabels"])
  },
  created() {
    this.steplabels = this.getStepLabels;
  },
  methods: {
    next() {
      // this.$store.commit("agent/setNextButton");

      const $this = this;
      console.log($this.step)
      if ($this.step === 1) {
        

        const personalFrmData = $this.$refs.PersonalInfo.submitForm();
        
        const stepOneResult = this.checkSteps(personalFrmData);
        stepOneResult
          .then(() => {
            $this.formData.agentFirstName = personalFrmData.firstName;
            $this.formData.agentLastName = personalFrmData.lastName;

            $this.formData.agentPhone = personalFrmData.phone;
            $this.formData.agentEmail = personalFrmData.email;
            $this.formData.agentBd = personalFrmData.birthDate;
            $this.formData.avatar = personalFrmData.avatar;
            $this.formError = "";
            console.log('First Step Form',$this.formData);
            // $this.step++;
            $this.$emit("onnext");
          })
          .catch((error) => {
            console.log(error);
            $this.formError = "some error exist";
          })
          .finally(() => (this.loading = false));
      }
      if ($this.step === 2) {
        console.log($this.formData);
        console.log('Submit Hfjgnfdjgngndjfnere');
        // console.log($this.$refs.FirmInfo.submitForm());
        if(typeof $this.$refs.FirmInfo!=='undefined'){
        const firmDataStr = $this.$refs.FirmInfo.submitForm();
        console.log("iiiiii")
        const stepOneResult = this.checkSteps(firmDataStr);
        console.log(stepOneResult)
        stepOneResult
          .then(() => {
            console.log("Its Under Then Callback");
            $this.formData.firm = firmDataStr.firm;
            $this.formData.firmAddress = firmDataStr.firmAddress;
            $this.formData.firmCity = firmDataStr.firmCity;
            $this.formData.firmState = firmDataStr.firmState;
            $this.formData.firmZip = firmDataStr.firmZip;
            $this.formData.licenseNumber = firmDataStr.licenseNumber;
            $this.formData.businessSince = firmDataStr.businessSince;
            $this.formData.BrokerAgreement = firmDataStr.BrokerAgreement;

            $this.$emit("onnext");
            $this.formError = "";

            localStorage.setItem("formDataAll", $this.formData);
            $this.SignUpAgent($this.formData);
            // $this.step++;
            // $this.$emit("onnext");
          })
          .catch((error) => {
            console.log(error);
            $this.formError = "some error exist";
          })
          .finally(() => (this.loading = false));
        }
      }
      // if ($this.step === 3) {
      //   const paymentFormData = JSON.parse($this.$refs.Payment.submitForm());
      //   const stepOneResult = this.checkSteps(paymentFormData);
      //   stepOneResult
      //     .then(() => {
      //       $this.$emit("onnext");
      //       $this.formError = "";

      //       $this.formData.cardName = paymentFormData.cardName;
      //       $this.formData.cardNumber = paymentFormData.cardNumber;
      //       $this.formData.expDate = paymentFormData.expDate;
      //       $this.formData.securityCode = paymentFormData.securityCode;
      //       $this.formData.cardAddress = paymentFormData.cardAddress;
      //       $this.formData.cardCity = paymentFormData.cardCity;
      //       $this.formData.cardState = paymentFormData.cardState;
      //       $this.formData.cardZip = paymentFormData.cardZip;

      //       localStorage.setItem("formDataAll", $this.formData);
      //       $this.SignUpAgent($this.formData);
      //     })
      //     .catch((error) => {
      //       console.log(error);
      //       $this.formError = "some error exist";
      //     })
      //     .finally(() => (this.loading = false));
      // }
    },

    checkSteps(formData) {
      const This = this;
      console.log(This.errors)
      this.errors = [];
      console.log("////CheckPromice");
      console.log(formData);
      return new Promise((resolve, reject) => {
        Object.keys(formData).forEach(function(key) {
          if (key === "name") {
            if (!This.isValidName(formData[key]))
              This.errors.push(key + " is not valid");
            //                                This.errors[key] = key + 'is not valid';
          }
          if (key === "email") {
            if (!This.isValidEmail(formData[key]))
              This.errors.push(key + " is not valid");
            //                            This.errors[key] = key + 'is not valid';
          }
        });
         
        if (This.errors.length > 0) {
          This.formError = "An error occurred";
          reject(new Error("An error occurred"));
        } else {
          console.log("no errors");
          resolve();
        }
      });
    },
    previous() {
      if (this.step > 1) {
        this.step--;
        console.log('pppppppppppp');
        this.$emit("onprevious");
        this.formError = "";
      }
    },
    getFormData(d) {
      console.log(d);
      this.formData.push(d);
    }
  }
};
</script>
