<template>
	<panel type="1" :show="this.$parent.achievementPanel" :close="close">
		<p></p>
		<p>未开发</p>
		<p> </p>
	</panel>
</template>

<script>
	import panel from './basicPanel.vue'
	export default {
		components:{
			panel
		},
		methods:{
			close(){this.$parent.set("achievementPanel",false)},
		}
	}
</script>
<style lang="less" scoped>
	
</style>