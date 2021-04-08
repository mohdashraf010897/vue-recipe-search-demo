<template>
  <div
    ref="intersection-ref"
    :style="{
      height: '100px',
      margin: '30px',
      padding: '50px',
    }"
  >
    <a-spin :spinning="isSpinning" size="large" tip="Fetching Results"></a-spin>
  </div>
</template>

<script>
export default {
  props: {
    isSpinning: { default: false },
    afterKey: { default: undefined },
    options: {
      type: Object,
      default: function() {
        return {
          root: document.getElementById("under-observation"),
          threshold: "1",
        };
      },
    },
    setAfter: { type: Function },
    scrollToTop: { default: false },
  },
  data() {
    return {
      observer: null,
    };
  },
  mounted() {
    this.observer = new IntersectionObserver((entries) => {
      this.handleIntersect(entries[0]);
    }, this.options);

    this.observer.observe(this.$refs["intersection-ref"]);
  },
  destroyed() {
    this.observer.disconnect();
  },

  updated() {
    this.scrollToTop &&
      document.getElementById("under-observation").scrollTo(0, 0);
  },
  methods: {
    handleIntersect(entry) {
      if (entry.isIntersecting) {
        if (this.isSpinning) return;
        this.setAfter(this.afterKey);
        this.$emit("triggerIntersected");
      }
    },
  },
};
</script>

<style></style>
