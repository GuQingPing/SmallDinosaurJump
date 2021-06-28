<template>
	<div id="bg">
		<div class="mountains"><span v-for="item in mStyles" :style="item"></span></div>
		<div id="ground"></div>
	</div>
</template>
<script>
	export default {
		name:'bg',
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
				cache:null,
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
		background:#91bef8;
		background-size: cover;
		width: 100%;height: 100%;
		left: 0;top: 0;
		transition: .5s;
	}
	@ground:40%;
	#ground {
		position: fixed;
		bottom: 0%;left: 0%;
		width: 100%;height: 40vh;
		background: linear-gradient(to bottom, #3a536b, #151e27);
		border: 0.3vh solid #000;
		transition: .5s;
	}
	.mountains{
		position: absolute;
		bottom:@ground;left: 0;
		width: 100%;
		height: 30vh;
		span{
			display: none;
			position: absolute;
			bottom: -1%;left: 0;
			background: rgb(56, 80, 103);
			height: 0;
		}
	}
	#bg.running span{display: inline-block; animation: bgMove 15s infinite linear both,bgShow 1s both;}
	#bg.paused span{display: none;}
	#bg.paused{
		background: #c4d1e1;
		#ground {
			background: #000;
		}
	}
	@keyframes bgMove{
		from{transform: translate(100vw,0);}
		to{transform: translate(-200vw,0);}
	}
	@keyframes bgShow{
		from{height: 0%;}
		to{height: 100%;}
	}
</style>
