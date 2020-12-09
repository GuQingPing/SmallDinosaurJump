<template>
	<div id="panel" @click="click">
		<div v-if="visible" class="ui">
			<div class="title"><span></span>小恐龙跳跃</div>
			<div class="btn start">开始游戏</div>
			<div class="btn shop">商城</div>
			<div class="btn achievement">成就</div>
			<div class="btn setting">设置</div>
			<div class="btn about">关于</div>
			<div class="version">{{version}}</div>
			<div class="record">最高记录:获取中...</div>
		</div>
		<div v-if="!visible" class="game">
			<div class="btnBox">
				<div class="btn sound">
					<audio id="jump" src="../assets/sound/jump.mp3"></audio>
					<audio id="bgm" src="../assets/sound/bgm.wav" autoplay loop></audio>
				</div>
				<div class="btn setting"></div>
				<div class="aliveTime">0:0:0:0</div>
				<div class="score">0</div>
			</div>
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
				version: "BetaV0.2.1",
			}
		},
		created() {//页面加载完成之前(组件诞生)
			document.addEventListener('keydown', this.keyDown)
		},
		mounted(){//页面加载完成之后
			let box=document.getElementsByClassName("record")[0];
			let score=localStorage.getItem("score")??0;
			box.innerHTML="最高记录:"+score;
		},
		destroyed() {document.removeEventListener('keydown',this.keyDown)},
		methods: {
			click(e) { //面板点击
				let c = e.target.classList;
				if (c.contains("start")) {
					this.visible = false;
				}
				if (c.contains("sound")) {
					if(c.contains("clicked")) c.remove("clicked");
					else c.add("clicked");
				}
			},
			keyDown(e){
				if (e.code=="Space") this.visible = false;
			},
		},
		components:{
			dinosaur,bg
		}
	}
</script>

<style scoped lang="less">
	#panel {
		width: 100%;height: 100%;
		.ui{
			background-size: contain;
			background:#e7e7e7;
			width: 100%;height: 100%;
			text-align: center;
			position: relative;
			box-sizing: border-box;
			user-select: none;
			padding-top: 7%;
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
				&:hover {
					filter: drop-shadow(1px 0px 0px #000);
				}
			}
			.record,.version {
				position: absolute;
				bottom: 2%;
				font-size: 150%;
				&.version{left: 2%;}
				&.record{right: 2%;}
			}
		}
		.game{
			user-select: none;
			.btn{
				@size:3%;
				display: inline-block;
				width: @size;padding-top: @size;
				overflow: hidden;
				margin: 1em;
				cursor: pointer;
				transition: .2s;
				border-radius: 50%;
				animation: fade-in 1s .3s both;
				position: relative;
				z-index: 2;
				&:hover{filter: invert(30%)}
				&.sound{
					position: fixed;left: 0;
					background: url(../assets/icon/声音开.svg) no-repeat center;
					background-size: contain;
					&.clicked{
						background: url(../assets/icon/声音关.svg) no-repeat center;
						background-size: contain;
					}
				}
				&.setting{
					position: fixed;right: 0;
					background: url(../assets/icon/设置.svg) no-repeat center;
					background-size: contain;
					&:hover{
						animation: fade-in 1s .3s both,rotate 2s infinite linear;
					}
				}
			}
			.score,.aliveTime{
				font-size: 2vw;
				position: fixed;top: 2.5%;
				width: 100%;
				text-align: center;
				z-index: 1;
				&.aliveTime{
					text-align: left;
					padding-left: 10%;
				}
			}
				
		}
	}
	@keyframes rotate{0%{transform: rotate(0deg)}100%{transform: rotate(360deg)}}
</style>
