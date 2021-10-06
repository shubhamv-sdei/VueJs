<template>
  <b-row class="stepper">
    <b-col class="round theme-font-semi-bold" @click="valuedecreased()">
      <span>-</span>
    </b-col>
    <b-col class="theme-font-semi-bold c">
      <span>{{ value + "+" }}</span>
    </b-col>
    <b-col class="round theme-font-semi-bold" @click="valueincreased()">
      <span>+</span>
    </b-col>
  </b-row>
</template>

<script>
export default {
  name: "Stepper",
  data() {
    return {
      value: 1
    };
  },
  methods: {
    valuedecreased() {
      if (this.value > 0) {
        this.value--;
        this.$emit("value-changed", this.value);
      }
    },
    valueincreased() {
      if (this.value < 50) {
        this.value++;
        this.$emit("value-changed", this.value);
      }
    }
  }
};
</script>
