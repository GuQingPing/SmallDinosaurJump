<template>
	<panel type="0" :show="this.$parent.failedPanel" class="failedPanel" :close="close">
		<p>你碰到刺了!!!</p>
		<p>存活时间: {{this.$parent.aliveTime}}</p>
		<p>最终分数: {{this.$parent.score}}</p>
		<p>历史最高分:{{this.$parent.record}}</p>
		<p style="text-transform: capitalize;">难度: {{this.$parent.difficulty}}</p>
		<div class="btnBox">
			<div @click="back" class="btn back">返回主菜单</div>
			<div @click="close" class="btn close">关闭</div>
			<div @click="playAgain" class="btn again">重来</div>
		</div>
	</panel>
</template>

<script>
	import panel from './basicPanel.vue'
	export default {
		components:{panel},
		methods:{
			playAgain(){this.$parent.playAgain()},
			back(){this.$parent.visible=true;this.$parent.failedPanel=false},
			close(){this.$parent.set("failedPanel",false)},
		}
	}
</script>

<style lang="less" scoped="scoped">
	.failedPanel{
		.close{bottom:80%;left:80%;}
		.again{
			--c1:limegreen;
			--c2:white;
			padding: .25em 2em;
			background: var(--c1);
			color: var(--c2);
		}
		.close{padding: .25em 2em;}
		.back{
			padding: .25em 4em;
		}
	}
</style>
