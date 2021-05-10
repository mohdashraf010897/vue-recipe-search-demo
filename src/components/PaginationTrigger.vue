<template>
  <a-row style="width:100%" type="flex" justify="center">
    <div
      ref="intersection-ref"
      :style="{
        height: '100px',
        margin: '30px',
        padding: '50px',
      }"
    >
      <a-spin
        :spinning="isSpinning"
        size="large"
        tip="Fetching Results"
      ></a-spin></div
  ></a-row>
</template>

<script>
export default {
	props: {
		isSpinning: {type:Boolean, default: false },
		options: {
			type: Object,
			default() {
				return {
					root: document.getElementById('under-observation'),
					threshold: '1',
				};
			},
		},/* eslint-disable no-console */
		callNextPage: { type: Function ,default() {
			return () => {
				console.log('Please provide a function');
			};
		}},/* eslint-enable no-console */
		scrollToTop: { type:Boolean,default: false },
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

		this.observer.observe(this.$refs['intersection-ref']);
	},
	destroyed() {
		this.observer.disconnect();
	},

	updated() {
		this.scrollToTop
      && document.getElementById('under-observation').scrollTo(0, 0);
	},
	methods: {
		handleIntersect(entry) {
			if (entry.isIntersecting) {
				/* eslint-disable no-console */
				
				console.log('hi')
				/* eslint-enable no-console */
				if (this.isSpinning) return;
				this.callNextPage();
				this.$emit('triggerIntersected');
			}
		},
	},
};
</script>

<style></style>
