<template>
  <div role="tablist" class="faqAccordion">
    <b-card v-for="faq in faqsData" :key="faq.id" no-body class="mb-1">
      <b-card-header header-tag="header" class="p-1" role="tab">
        <b-button
          block
          href="#"
          :class="faq.visible ? null : 'collapsed'"
          :aria-expanded="faq.visible ? 'true' : 'false'"
          :aria-controls="'faq-' + faq.id"
          variant="info"
          @click="faq.visible = !faq.visible"
          >{{ faq.title }}</b-button
        >
      </b-card-header>
      <b-collapse
        :id="'faq-' + faq.id"
        v-model="faq.visible"
        accordion="faq-accordion"
        role="tabpanel"
      >
        <b-card-body>
          <div class="card-text" v-html="faq.text"></div>
        </b-card-body>
      </b-collapse>
    </b-card>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: "Faq",
  props: {
    faqsData: {
      type: Array,
      default: () => {
        return [];
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
