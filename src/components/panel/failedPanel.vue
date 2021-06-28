<template>
	<panel type="0" :show="panel.failedPanel" class="failedPanel" :close="close">
		<p>你碰到刺了!!!</p>
		<p>存活时间: {{panel.aliveTime}}</p>
		<p>存活得分: {{panel.score-game.eatGolds*10}}({{Math.ceil(game.ms/100)}}x{{parseInt(1+(game.s/20))}}x{{game.difficultyScoreAmend}})</p>
		<p>金币得分:{{game.eatGolds*10}}({{game.eatGolds}}x10)</p>
		<p>最终分数: {{panel.score}}</p>
		<p>历史最高分:{{panel.record}}</p>
		<p style="text-transform: capitalize;">难度: {{panel.difficulty}}</p>
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
		data(){
			return{
				panel:this.$parent,
				game:this.$parent.get("game"),
			}
		},
		methods:{
			playAgain(){this.$parent.playAgain()},
			back(){this.$parent.visible=true;this.$parent.autoBack();},
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
