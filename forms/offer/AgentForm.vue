<template>
  <b-row>
    <b-col md="6" class="mx-auto agent-form">
      <hr />
      <h4 class="font-weight-bold mt-5">Agent</h4>
      <b-row>
        <b-col cols="12" md="8" lg="7" xl="6" class="mt-4 p-0">
          <div class="title">
            Do you want to use the same agent?
          </div>
          <div class="agent-content-holder">
            <div
              class="agent-image mx-auto shadow"
              :style="{
                backgroundImage:
                  'url(\'' + require('@/assets/images/agent/agent.png') + '\')'
              }"
            ></div>
            <span class="agent-name text-center shadow-sm">Tom Haverford</span>
          </div>
        </b-col>
      </b-row>
      <b-row>
        <b-col cols="12" md="10" lg="7" xl="6" class="p-0">
          <div class="switch-buttons mt-5">
            <b-row>
              <b-col md="6" class="p-0">
                <b-button
                  v-model="preApprovalLetterPath"
                  variant="primary"
                  class="switch-button no-right-radius shadow-none"
                  :class="{ selected: preApprovalLetterPath === 'Yes' }"
                  @click="setSameAgentToYes"
                  >Yes</b-button
                >
              </b-col>
              <b-col md="6" class="p-0">
                <b-button
                  v-model="preApprovalLetterPath"
                  variant="primary"
                  class="switch-button no-left-radius shadow-none"
                  :class="{ selected: preApprovalLetterPath === 'No' }"
                  @click="setSameAgentToNo"
                  >No</b-button
                >
              </b-col>
            </b-row>
          </div>
        </b-col>
      </b-row>
    </b-col>
    <b-row>
      <b-col md="6" class="p-0 mx-auto">
        <hr />
      </b-col>
    </b-row>
  </b-row>
</template>

<script>
export default {
  name: "AgentForm",
  computed: {
    offerFormInputs() {
      return this.$store.state.offerFormInputs;
    },
    preApprovalLetterPath: {
      get() {
        return this.$store.state.offerFormInputs.preApprovalLetterPath;
      },
      set(val) {
        this.offerFormInputs.preApprovalLetterPath = val;
        this.$store.commit("setOfferFormInputs", this.offerFormInputs);
      }
    }
  },
  methods: {
    setSameAgentToYes() {
      this.preApprovalLetterPath = "Yes";
    },
    setSameAgentToNo() {
      this.preApprovalLetterPath = "No";
    }
  }
};
</script>

<style scoped></style>
