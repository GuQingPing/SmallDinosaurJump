<template>
	<div v-if="failedPanel" class="allPanel failedPanel">
		<p>你碰到刺了!!!</p>
		<p>存活时间: {{aliveTime}}</p>
		<p>最终分数: {{score}}</p>
		<p>历史最高分:{{record}}</p>
		<p style="text-transform: capitalize;">难度: {{difficulty}}</p>
		<div @click="back" class="btn back">返回主菜单</div>
		<div @click="playAgain" class="btn again">重来</div>
		<div @click="close" class="btn close">关闭</div>
	</div>
</template>

<script>
	export default {
		props:{
			visible:{},
			failedPanel:{},
			aliveTime:{},
			score:{},
			difficulty:{},
		},
		data(){
			return{
				record:0,
			}
		},
		updated() {//获取历史最高分
			this.record=this.$parent.record;
		},
		methods:{
			playAgain(){this.$parent.playAgain()},
			back(){this.$parent.visible=true;this.$parent.failedPanel=false},
			close(){this.$parent.failedPanel=false},
		}
	}
</script>

<style lang="less" scoped="scoped">
	.failedPanel{
		.close{bottom:80%;left:80%;}
		.again{
			--c1:limegreen;
			--c2:white;
			position: absolute;
			bottom: 5%;right: 5%;
			padding: .25em 2.4em;
			background: var(--c1);
			color: var(--c2);
		}
		.back{
			position: absolute;
			bottom: 5%;left: 5%;
			padding: .25em 5em;
		}
	}
</style>
