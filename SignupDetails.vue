<template>
  <b-row>
    <b-modal ref="signUpDetails" size="md" centered hide-footer>
      <template v-slot:modal-title class="text-center">
        What’s your name and email?
      </template>
      <div class="d-block text-left mt-4">
        <div class="form-row">
          <div class="form-group col-md-6">
            <b-input
              v-model="firstName"
              class="sb theme-font-light"
              type="text"
              maxlength="20"
              placeholder="First name"
              :style="
                firstNameError ? 'border-color:red;' : ' border-color: #f0f0f0;'
              "
              autocomplete="off"
            />
          </div>
          <div class="form-group col-md-6">
            <b-input
              v-model="lastName"
              class="sb theme-font-light"
              type="text"
              maxlength="20"
              placeholder="Last name"
              :style="
                lastNameError ? 'border-color:red;' : ' border-color: #f0f0f0;'
              "
            />
          </div>
        </div>
        <div class="form-row">
          <div class="form-group col-md-12">
            <b-input
              v-model="email"
              class="sb2 theme-font-light"
              type="text"
              maxlength="50"
              placeholder="Email"
              :style="
                emailError ? 'border-color:red;' : ' border-color: #f0f0f0;'
              "
            />
          </div>
        </div>
      </div>
      <div class="row">
        <div class="col-md-12 text-right mt-3">
          <button class="mt-3 btn btn-primary" @click="next()">
            Create account
          </button>
        </div>
      </div>
    </b-modal>
  </b-row>
</template>

<script>
export default {
  name: "SignupDetails",
  data() {
    return {
      firstName: "",
      lastName: "",
      email: "",
      firstNameError: false,
      lastNameError: false,
      emailError: false
    };
  },
  mounted() {
    this.$refs.signUpDetails.show();

    this.$root.$on("bv::modal::hide", (bvEvent, modalId) => {
      this.close();
    });
  },
  methods: {
    next() {
      if (this.firstName === "") this.firstNameError = true;
      else if (this.lastName === "") this.lastNameError = true;
      else if (this.email === "" || !this.isvalidemail())
        this.emailError = true;
      else {
        const user = {
          firstName: this.firstName,
          lastName: this.lastName,
          email: this.email
        };
        this.firstName = "";
        this.lastName = "";
        this.email = "";
        this.firstNameError = false;
        this.lastNameError = false;
        this.emailError = false;
        this.$emit("onnext", user);
      }
    },
    close() {
      this.firstName = "";
      this.lastName = "";
      this.email = "";
      this.firstNameError = false;
      this.lastNameError = false;
      this.emailError = false;
      this.$emit("onclose");
    },
    isvalidemail() {
      const re = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      // var re = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return re.test(String(this.email).toLowerCase());
    }
  }
};
</script>
