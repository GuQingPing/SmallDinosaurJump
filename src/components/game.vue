<template>
	<div v-show="!visible" class="game">
		<div class="btnBox">
			<div class="btn sound">
				<div>
					<audio id="jump" src="../assets/sound/jump.mp3"></audio>
					<audio v-if="!visible" @loadstart="setVolume" id="bgm" src="../assets/sound/bgm.mp3" autoplay loop></audio>
					<audio id="click" src="../assets/sound/click.wav"></audio>
					<audio id="hover" src="../assets/sound/hover.mp3"></audio>
					<audio id="failed" src="../assets/sound/failed.mp3"></audio>
					<audio id="falled" src="../assets/sound/falled.mp3"></audio>
				</div>
			</div>
			<div class="btn setting"></div>
		</div>
		<div class="gameInfo">
			<div class="aliveTime">{{aliveTime}}</div>
			<div class="score">{{score}}</div>
			<div class="version">
				<p>难度:{{difficulty}}</p>
				<p>WASD ↑↓←→ 空格</p>
			</div>
		</div>
		<div v-if="!visible">
			<bg></bg>
			<cactus></cactus>
			<dinosaur></dinosaur>
		</div>
	</div>
</template>

<script>
	import dinosaur from './game/dinosaur.vue'
	import bg from './game/bg.vue'
	import cactus from './game/cactus.vue'
	export default {
		components:{dinosaur,bg,cactus},
		name:"game",
		methods:{
			setVolume(){this.$parent.setVolume()},
		},
		props:{
			visible:{},
			aliveTime:{},
			score:{},
			difficulty:{}
		},
	}
</script>

<style lang="less" scoped>
	@min:1366px;
	.game {
		min-width: @min;
		user-select: none;
		.btn {
			@size: 3%;
			display: inline-block;
			width: @size;
			padding-top: @size;
			overflow: hidden;
			margin: 1em;
			cursor: pointer;
			transition: .2s;
			border-radius: 50%;
			position: relative;
			z-index: 2;
			&:hover {
				filter: invert(30%)
			}
			&.sound {
				position: fixed;
				left: 0;
				background: url(../assets/icon/声音开.svg) no-repeat center;
				background-size: contain;
	
				&.clicked {
					background: url(../assets/icon/声音关.svg) no-repeat center;
					background-size: contain;
				}
			}
			&.setting {
				position: fixed;
				right: 0;
				background: url(../assets/icon/设置.svg) no-repeat center;
				background-size: contain;
				&:hover {
					animation: fade reverse 1s both, rotate 2s infinite linear;
				}
			}
		}
		.btn,.gameInfo{animation: fade reverse .5s;}
		.gameInfo {
			position: relative;
			z-index: 1;
			.score,
			.aliveTime {
				font-size: 2vw;
				position: fixed;
				top: 2.5%;
				width: 100%;
				text-align: center;
				&.aliveTime {
					text-align: left;
					padding-left: 10%;
				}
			}
			.version {
				text-transform: capitalize;
				color: #d4d4d4;
				position:fixed;
				bottom: 2%;left: 2%;
			}
		}
	}
</style>
