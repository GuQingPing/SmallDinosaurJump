<template>
	<panel type="1" :show="this.$parent.infoPanel" :close="close">
		<p>作者:古清平</p>
		<p>版本:{{this.$parent.version}}</p>
		<p>难度:{{this.$parent.difficulty}}</p>
		<p>模式:{{this.$parent.model}}</p>
		<p>更新时间:{{this.$parent.date}}</p>
		<p>联系QQ:2846913281</p>
	</panel>
</template>

<script>
	import panel from './basicPanel.vue'
	export default {
		components:{
			panel
		},
		methods:{
			close(){this.$parent.set("infoPanel",false)},
		}
	}
</script>