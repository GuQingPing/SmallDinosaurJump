<template>
	<div id="panel" @click="click($event)">
		<div v-if="visible" class="ui">
			<div class="title"><span></span>小恐龙跳跃</div>
			<div class="btn start">开始游戏</div>
			<div class="btn shop">商城</div>
			<div class="btn achievement">成就</div>
			<div class="btn setting">设置</div>
			<div class="btn about">关于</div>
			<div class="version">{{version}}</div>
			<div class="record">最高记录:0</div>
		</div>
		<div v-if="!visible" class="game">
			<bg></bg>
			<dinosaur></dinosaur>
		</div>
	</div>
</template>

<script>
	import dinosaur from './dinosaur.vue'
	import bg from './bg.vue'
	export default {
		name: "panel",
		data() {
			return {
				visible: true,
				version: "BetaV0.1.0",
			}
		},
		methods: {
			click(e) { //面板点击
				let c = e.target.classList;
				if (c.contains("start")) {
					this.visible = false;
					document.body.classList.add("game");
				}
			},
		},
		components:{
			dinosaur,bg
		}
	}
</script>

<style scoped lang="less">
	#panel {width: 100%;height: 100%;}
	.ui{
		background-size: contain;
		background:#e7e7e7;
		width: 100%;height: 100%;
		text-align: center;
		position: relative;
		box-sizing: border-box;
		user-select: none;
		padding-top: 7%;
	}
	.title{
		background: url(../assets/img/dinosaur.png) no-repeat;
		background-position: calc(50% - 3em);
		background-size: contain;
		font-size: 300%;
		margin-bottom: 4%;
	}
	.btn {
		cursor: pointer;
		font-size: 200%;
		padding: .2em 0;
		width: 20%;
		margin: 0 auto;
		margin-bottom: .5em;
		border-radius: 5px;
		background-color: rgba(0, 0, 0, .1);
		transition: .2s;
	}
	.btn:hover {
		filter: drop-shadow(1px 0px 0px #000);
	}
	.record,
	.version {
		position: absolute;
		bottom: 2%;
		font-size: 150%;
	}
	.record {
		right: 2%;
	}
	.version {
		left: 2%;
	}
</style>
