<template>
  <div class="customers-comp">
    <div class="row secondrow">
      <div class="col-sm-12 col-md-6 col-xl-3 col-lg-3 cstrCol">
        <h1 class="main-title" v-html="data.header"></h1>
      </div>
      <div
        v-for="(item, index) in data.items"
        :key="index"
        class="col-sm-12 col-md-6 col-xl-3 col-lg-3 cstrCol"
      >
        <div class="card text-center">
          <div class="card-body">
            <img
              :src="'/assets/icons/customers/' + item.icon"
              alt=""
              class="card-icon"
            />
            <h5 class="card-title">{{ item.title }}</h5>
            <p class="card-text">
              {{ item.text }}
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "CustomersSection",
  props: {
    data: {
      type: Object,
      default: () => {
        return {};
      }
    }
  },
  data() {
    return {
      slider: 450000
    };
  },
  computed: {
    computedSlider: {
      get() {
        return this.numberWithCommas(this.slider);
      },
      set(val) {
        this.slider = this.numberWithoutCommas(val);
      }
    }
  },
  methods: {
    numberWithCommas(x) {
      return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
    numberWithoutCommas(x) {
      return x.toString().replace(/,/g, "");
    },
    calc2(small, big) {
      const a = small;

      const b = big;

      const c = a / b;

      const d = c * 100;

      return d;
    }
  }
};
</script>
