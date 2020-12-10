<template>
	<div id="bg">
		<div id="ground">
			<cactus></cactus>
			<div class="mountains">
				<span v-for="item in mStyles" :style="item"></span>
			</div>
		</div>
	</div>
</template>
<script>
	import cactus from './cactus.vue'
	export default {
		name: 'bg',
		components:{cactus},
		created() {//初始化-生成山脉
			let s = "";
			let lastWidhth = 0;
			for (var i = 0; i < 40; i++) {
				let createRandom=this.createRandom;
				let width = createRandom(5, 20);
				let height = createRandom(50, 100);
				let radius = createRandom(40, 50);
				let radius2 = createRandom(40, 50);
				let other ="";
				if(i>10) other="animation-delay:5s;";
				if(i>20) other="animation-delay:10s;";
				s += `width:${width}%;height:${height}%;border-radius:${radius}% ${radius2}% 0 0;left:${lastWidhth}%;${other}`;
				this.mStyles.push(s);
				lastWidhth += width - 1;
				if(i%10==0) lastWidhth=0;
			}
		},
		data() {
			return {
				mStyles: new Array(),
			}
		},
		methods:{
			createRandom(start, end) {//生成随机数
				let abs = Math.abs(start - end);
				return Math.round(start + Math.round(Math.random() * abs));
			}
		}
	}
</script>
<style lang="less" scoped>
	#bg{
		width: 100%;height: 100%;
		position: fixed;
		background:linear-gradient(to bottom,#91bef8,#c4d1e1) center repeat;
		background-size: cover;
		width: 100%;height: 100%;
		left: 0;top: 0;
	}
	#ground {
		position: fixed;
		bottom: 40%;
		left: 0%;
		width: 100%;
		height: 3px;
		background-color: #000;
		animation: scale 1s both;
		z-index: 0;
		&::before {
			content: '';
			position: absolute;
			width: 100%;
			padding-top: 45vh;
			top: 100%;
			left: 0;
			background: linear-gradient(to bottom, #3a536b, #151e27);
			z-index: 0;
		}
		.mountains{
			position: absolute;
			top: -30vh;left: 0;
			width: 100%;
			height: 30vh;
			z-index: -2;
			span{
				display: none;
				position: absolute;
				bottom: -1%;left: 0;
				display: inline-block;
				border-bottom: 1px solid #000;
				background: rgb(56, 80, 103);
				animation: bgMove 15s infinite linear both;
			}
		}
	}
	@keyframes scale {
		0% {transform: scale(1, 0);}
		100% {transform: scale(1, 1);}
	}
	@keyframes bgMove{
		0% {transform: translate(100vw,0);}
		100% {transform: translate(-200vw,0);}
	}
</style>
