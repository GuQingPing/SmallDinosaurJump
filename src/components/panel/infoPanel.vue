<template>
	<div v-if="infoPanel" class="allPanel infoPanel">
		<span v-html="infoPanelText"></span>
		<div @click="close" class=" btn close">关闭</div>
	</div>
</template>

<script>
	export default {
		props:{
			infoPanel:{},
			infoPanelText:{},
		},
		methods:{
			close(){this.$parent.infoPanel=false},
		}
	}
</script>
<style></style>
