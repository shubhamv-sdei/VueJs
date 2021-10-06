<template>
  <div>
    <b-row>
      <span class="heading theme-font-bold">{{ getFormTitle }}</span>
    </b-row>

    <b-form class="userInfoForm" @submit.prevent="submitForm">
      <b-form-row>
        <div class="input-avatar">
          <label for="file-input">
            <img v-if="form.avatar" class="avatarTempImg" :src="form.avatar" />
            <img v-else class="userIcon" src="@/assets/icons/user.svg" />
          </label>
          <input
            id="file-input"
            name="profile-img"
            type="file"
            accept="image/x-png, image/gif, image/jpeg"
            @change="avatarCheck"
          />
        </div>
        <span>Upload a profile photo</span>
      </b-form-row>
      <div class="form-row desktop-view">
        <div class="col-md-12">
          <div
            v-if="avatarError"
            class="invalid-feedback"
            :style="{
              display: avatarError ? 'block' : 'none'
            }"
          >
            Please upload your profile image for verification.
          </div>
        </div>
      </div>
      <b-form-row>
        <b-form-group
          id="user-firstName"
          label="Name"
          label-for="firstName"
          class="col-md-6"
        >
          <b-form-input
            id="firstName"
            v-model="form.firstName"
            type="text"
            :class="
              formErrors.indexOf('firstName') !== -1 ? 'is-invalid' : 'val'
            "
            placeholder="E.g John"
            required
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('firstName') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid name. Eg John.
          </div>
        </b-form-group>
        <b-form-group
          id="user-lastName"
          label="Last name"
          label-for="lastName"
          class="col-md-6"
        >
          <b-form-input
            id="lastName"
            v-model="form.lastName"
            type="text"
            :class="
              formErrors.indexOf('lastName') !== -1 ? 'is-invalid' : 'val'
            "
            placeholder="E.g Doe"
            required
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('lastName') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid last name. Eg Doe.
          </div>
        </b-form-group>
      </b-form-row>

      <b-form-row>
        <div
          id="user-phone"
          label="Phone"
          class="col-md-6 form-group"
          label-for="email"
        >
          <label for="phone" class="d-block">Phone</label>
          <div class="phoneVerified">
            <input
              id="phone"
              v-model="orgPhoneNumber"
              type="text"
              class="form-control"
              disabled
            />
            <div class="verified">
              <img src="@/assets/icons/check2.svg" alt="" /> Verified
            </div>
          </div>
        </div>

        <b-form-group
          id="user-email"
          label="Email"
          class="col-md-6"
          label-for="phone"
        >
          <b-form-input
            id="email"
            v-model="form.email"
            type="email"
            pattern="[a-zA-Z0-9._%+-+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
            :class="formErrors.indexOf('name') !== -1 ? 'is-invalid' : ''"
            placeholder="Enter your email address"
            required
          ></b-form-input>
          <div
            v-if="formErrors.indexOf('name') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid email address.
          </div>
        </b-form-group>
      </b-form-row>
      <b-form-row>
        <b-form-group
          id="user-bd"
          label="Birthdate"
          class="col-md-6"
          label-for="birth-date"
        >
          <b-form-input
            id="birth-date"
            v-model="form.birthDate"
            v-format-date
            :class="formErrors.indexOf('birthDate') !== -1 ? 'is-invalid' : ''"
            placeholder="E.g 02/15/1995"
            type="text"
            min="01/01/1940"
            required
            size="10"
            maxlength="10"
          ></b-form-input>
          <!-- <datepicker
            format="MM/dd/yyyy"
            placeholder="21221"
            @selected="selectBirthDate($event)"
            :bootstrap-styling="true"
            :disabled-dates="disabledDates"
            :typeable="true"
            :open-date="minAge"
            :required="true"
            wrapper-class="date-pic"
          ></datepicker> -->
          <div
            v-if="formErrors.indexOf('birthDate') !== -1"
            class="invalid-feedback"
          >
            Please provide a valid birthday.
          </div>
        </b-form-group>
      </b-form-row>

      <div class="separator endSep"></div>
      <b-form-row>
        <b-col cols="6" class="text-left">
          <button
            disabled
            class="btn theme-font-semi-bold btn-default disabled minsize"
          >
            Back
          </button>
        </b-col>
        <b-col cols="6" class="text-right">
          <button
            class="btn btn-primary theme-font-semi-bold minsize"
            type="button"
            @click="onSubmitWithEm"
          >
            Next
          </button>
        </b-col>
      </b-form-row>
      <div class="form-row mobile-view">
        <div class="col-md-12 mt-3">
          <div
            v-if="avatarError"
            class="invalid-feedback"
            :style="{
              display: avatarError ? 'block' : 'none'
            }"
          >
            Please upload your profile image for verification.
          </div>
        </div>
      </div>
    </b-form>
    <b-overlay :show="show" variant="light" no-wrap> </b-overlay>
  </div>
</template>

<script>
// import Uploader from "@/plugins/services/s3ImageUpload";
import axios from "axios";
import { mapState, mapGetters, mapMutations } from "vuex";
import { Helper } from "@/plugins/mixins/helper";
// import Datepicker from "vuejs-datepicker";

export default {
  name: "PersonalInfo",
  components: {
    // Datepicker
  },
  mixins: [Helper],
  data() {
    return {
      form: {
        avatar: "",
        firstName: "",
        lastName: "",
        phone: null,
        email: "",
        birthDate: "",
        tempImage: ""
      },
      show: false,
      uploadedImage: "",
      emailCheck: false,
      phoneCheck: false,
      nameCheck: false,
      phoneValue: 0,
      avatarError: false,
      orgPhoneNumber: "",
      options: {
        phone: true,
        phoneRegionCode: "US",
        prefix: "+1"
      },
      formErrors: []
      // disabledDates: {
      //   from: new Date(new Date().getFullYear() - 20, 1, 1)
      // },
      // minAge: new Date(new Date().getFullYear() - 21, 12, 31)
    };
  },

  computed: {
    ...mapState("modules/agent", [
      "errors",
      "step",
      "nextButton",
      "formData",
      "stepOneForm"
    ]),

    ...mapGetters("modules/agent", [
      "activelabel",
      "getStepLabels",
      "getFormTitle"
    ]),
    getPhoneNumber() {
      return this.updatePhoneNumber();
    }
  },
  watch: {
    $route() {
      this.form.phone = localStorage.getItem("phoneNumber");
    }
  },
  mounted() {
    this.form.phone = localStorage.getItem("phoneNumber");
    this.orgPhoneNumber = localStorage.getItem("phoneNumber");
  },
  created() {
    if (!this.isEmpty(this.stepOneForm)) {
      this.form = this.stepOneForm;
    }
    // this.form.phone = this.getPhoneNumber;
  },
  methods: {
    ...mapMutations("modules/agent", ["setStep", "setFormFields"]),
    onSubmitWithEm() {
      this.$emit('finished');
    },
    submitForm() {
      const This = this;
      const errs = [];
      This.formErrors = [];
    
      Object.keys(This.form).forEach(function(key) {
        if (key === "firstName") {
          if (!This.isValidName(This.form[key])) errs.push(key);
        }
        if (key === "avatar") {
          This.avatarError = true;
          if (This.form.avatar === "") errs.push(key);
        }
        if (key === "lastName") {
          if (!This.isValidName(This.form[key])) errs.push(key);
        }
        if (key === "email") {
          if (!This.isValidEmail(This.form[key])) errs.push(key);
        }
        if (key === "birthDate") {
          if (!This.isValidBirthDay(This.form[key])) {
            errs.push(key);
          }
        }
      });
      if (errs.length > 0) {
        This.formErrors = errs;
        console.log(errs)
      } else {
        This.setStep(1);
        This.setFormFields({ stepNumber: "stepOneForm", fields: This.form });
        console.log(This.form)
        return This.form;
      }
    },

    checkStepss(formData) {
      const This = this;
      const errs = [];
      This.formErrors = [];

      return new Promise((resolve, reject) => {
        Object.keys(formData).forEach(function(key) {
          if (key === "name") {
            if (!This.isValidName(formData[key]))
              errs.push(key + " is not valid");
          }
          if (key === "email") {
            if (!This.isValidEmail(formData[key]))
              errs.push(key + " is not valid");
          }
        });

        if (errs.length > 0) {
          This.formErrors = errs;
          reject(new Error("Error"));
        } else {
          resolve();
        }
      });
    },
    async avatarCheck(e) {
      const $this = this;
      $this.show = true;
      console.log(e.target.value);
      if (e.target.value) {
        const $event = e;
        const ext = e.target.files[0].name.split(".")[
          e.target.files[0].name.split(".").length - 1
        ];
        const fileName =
          localStorage.getItem("formattedPhoneNumber") + "." + ext;
        try {
          const reqHeader = new Headers();
          reqHeader.append("Content-Type", "application/json");
          const raw = JSON.stringify({
            object_key: "profile/" + fileName
          });
          const requestOptions = {
            method: "POST",
            headers: reqHeader,
            body: raw,
            redirect: "follow"
          };
          await fetch(
            "https://f9vnl5y816.execute-api.us-west-2.amazonaws.com/prod",
            requestOptions
          )
            .then((response) => response.text())
            .then((result) => {
              const URL = JSON.parse(result).url;

              const data = $event.target.files[0];
              axios.put(URL, data).then((e) => {
                $this.avatarError = false;
                $this.uploadedImage =
                  "https://nexme-upload-images.s3.us-west-2.amazonaws.com/profile/" +
                  fileName +
                  "?" +
                  Math.floor(Math.random(99999999) * 979855854) +
                  1;
                $this.form.avatar =
                  "https://nexme-upload-images.s3.us-west-2.amazonaws.com/profile/" +
                  fileName;
                $this.show = false;
              });
            })
            .catch((error) => {
              $this.show = false;
              console.log("error", error);
            });
        } catch (error) {
          $this.show = false;
          console.log(error);
        }
      }
    },
    onReset(evt) {
      evt.preventDefault();
      this.form.email = "";
      this.form.avatar = "";
      this.form.name = "";
      this.form.phone = "";
      this.form.birthDate = "";
    },
    NumberCheck(d) {
      this.phoneCheck = this.isNumber(d);
    },
    EmailPatternCheck(d) {
      this.emailCheck = this.EmailCheck(d);
    },
    checkName(evt) {
      console.log(evt);
    },
    errorCheck(data, input) {
      for (let i = 0; i < data.length; i++) {
        if (data[i] === input) {
          return true;
        } else {
          return false;
        }
      }
    },
    selectBirthDate(d) {
      if (d) {
        this.form.birthDate = `${d.getMonth() +
          1}/${d.getDate()}/${d.getFullYear()}`;
      }
    }
  }
};
</script>
