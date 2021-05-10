<template>
  <a-card
    hoverable
    :style="{
      width: '100%',
      border: ' 1px solid #cf1421',
      borderRadius: '5px',
    }"
    :body-style="{ padding: '10px 10px 0' }"
  >
    <a-row
      type="flex"
      justify="space-between"
      align="middle"
      :style="{ minHeight: '40px', flexWrap: 'wrap', gridGap: '10px', flex: 1 }"
    >
      <h3
        :style="{
          height: 'auto',
          whiteSpace: 'pre-wrap',
          marginBottom: '0px',
          lineHeight: '15px',
          fontSize: '14px',
          color: '#ff7f7f',
          display: 'flex',
          alignItems: 'center',
          gridGap: '7px',
          textAlign: 'left',
        }"
      >
        <img
          :style="{
            transform: 'rotate(-25deg)',
            height: '30px',
            position: 'relative',
            top: '1px',
          }"
          src="https://img.icons8.com/emoji/48/000000/speaker-medium-volume.png"
        />
        <span>{{ promotedItem.title.trim().replace(/[()]/g, "") }}</span>
      </h3>
      <a-tag
        :color="'red'"
        :style="{
          fontSize: '14px',
          textTransform: 'uppercase',
          fontWeight: '600',
          borderRadius: '40px',
          padding: '2px 8px',
          lineHeight: 'unset',
          marginRight: 0,
        }"
        >Promoted</a-tag
      >
    </a-row>

    <!-- footer -->
    <a-row
      type="flex"
      justify="space-between"
      align="bottom"
      :style="{
        padding: '10px 0',
        textAlign: 'left',
        lineHeight: '15px',
        flexWrap: 'nowrap',
      }"
      ><span>
        Sponsored by :
        <a-tooltip title="See Full Recipe!">
          <a :href="promotedItem.link" target="_blank" rel="noreferrer">
            {{ getHostname(promotedItem.link) }}
          </a>
        </a-tooltip>
      </span>
      <i> Powered by Query Rules</i>
    </a-row>
  </a-card>
</template>

<script>
export default {
	name: 'PromotedCardItem',
	props: {
		promotedItem: {type:Object, default(){return {}}},
	},
	methods: {
		log(promotedItem) {
			console.log(promotedItem);
		},
		getHostname(url) {
			let urlLocal = url;
			let tempProtocol = '';
			// use URL constructor and return hostname
			if (urlLocal.indexOf('://') == -1) {
				tempProtocol = 'https://';
				urlLocal = tempProtocol + urlLocal;
			}
			return new URL(urlLocal).hostname.split('.')[1];
		},
	},
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
i {
  text-align: right;
}
a {
  color: #42b983;
}
</style>
