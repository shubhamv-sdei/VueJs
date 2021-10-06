<template>
  <div role="tablist" class="faqAccordion">
    sdfsdfsdfsdfsdfsdfsdf
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: "BrokerAgreementContent",
  props: {
    faqsData: {
      type: Object,
      default: () => {
        return {};
      }
    },
    someText: {
      type: String,
      default: ""
    }
  },
  data() {
    return {
      faqs: {}
    };
  }
};
</script>
