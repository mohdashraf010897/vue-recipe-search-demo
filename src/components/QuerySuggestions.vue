<template>
  <a-row type="flex" :style="{ rowGap: '10px', marginTop: '1rem' }">
    <span style="margin:0 5px">Try:</span>
    <template v-for="(item, index) in STATIC_RECIPE_SUGGESTIONS">
      <a-tag
        :key="index"
        :color="item.tagColor"
        :style="{ cursor: 'pointer' }"
        @click="setQueryValue(item.value)"
        >{{ item.value }}</a-tag
      >
    </template>
  </a-row>
</template>

<script>
export default {
	inject: ['searchbase'],
	data() {
		return {
			STATIC_RECIPE_SUGGESTIONS: [
				{ value: 'Pizza', tagColor: 'magenta' },
				{ value: 'Curry', tagColor: 'red' },
				{ value: 'Low Carb', tagColor: 'volcano' },
				{ value: 'Pasta', tagColor: 'orange' },
				{ value: 'Fried', tagColor: 'gold' },
				{ value: 'Diabetic', tagColor: 'lime' },
				{ value: 'Veg', tagColor: 'green' },
				{ value: 'Healthy', tagColor: 'geekblue' },
				{ value: 'Saute', tagColor: 'cyan' },
			],
		};
	},
	mounted() {
		console.log('HIHIHI', this.searchbase);
	},
	methods: {
		setQueryValue(itemName) {
			this.searchbase._components['search-component'].setValue(itemName, {
				triggerCustomQuery: true,
			});
		},
	},
};
</script>

<style></style>
