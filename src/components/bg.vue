<template>
	<div id="bg">
		<div id="ground">
			<div class="mountains">
				<span v-for="item in mStyles" :style="item"></span>
			</div>
		</div>
	</div>
</template>
<script>
	export default {
		name: 'bg',
		created() {//初始化-生成山脉
			let s = "";
			let lastWidhth = 0;
			for (var i = 0; i < 30; i++) {
				let width = createRandom(5, 20);
				let height = createRandom(50, 100);
				let radius = createRandom(40, 50);
				let radius2 = createRandom(40, 50);
				let name="rush";
				let delay ="";
				if(i>10) delay="animation-delay:3.5s;";
				if(i>20) delay="animation-delay:7s;";
				s += `width:${width}%;height:${height}%;border-radius:${radius}% ${radius2}% 0 0;left:${lastWidhth}%;${delay}`;
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
	}
	//生成随机数
	function createRandom(start, end) {
		let abs = Math.abs(start - end);
		return Math.round(start + Math.round(Math.random() * abs));
	}
</script>
<style lang="less" scoped>
	#bg {
		background:linear-gradient(to bottom,#91bef8,#c4d1e1) center no-repeat;
		position: absolute;
		width: 100%;
		height: 100%;
		left: 0;
		top: 0;
	}

	#ground {
		position: fixed;
		bottom: 40%;
		left: 0%;
		width: 100%;
		height: 3px;
		background-color: #000;
		animation: scaleHeight 1s both;
	}

	#ground::before {
		content: '';
		position: absolute;
		width: 100%;
		padding-top: 45vh;
		top: 100%;
		left: 0;
		background: linear-gradient(to bottom, #3a536b, #151e27);
		z-index: -1;
	}

	.mountains,
	.catus {
		position: absolute;
		top: -30vh;
		left: 0;
		width: 100%;
		height: 30vh;
		z-index: -2;
	}

	.mountains span {
		position: absolute;
		bottom: 0;
		left: 0;
		display: inline-block;
		background: rgb(56, 80, 103);
		animation: rush 10s infinite linear both;
	}

	@keyframes scaleHeight {
		0% {transform: scale(1, 0);}
		100% {transform: scale(1, 1);}
	}
	@keyframes rush{
		0% {transform: translate(100vw,0);}
		100% {transform: translate(-200vw,0);}
	}
</style>
