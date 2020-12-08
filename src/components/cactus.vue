<template>
	<div class="cactus">
		<span v-for="i in cactus" :style="i.style" :class="i.class"></span>
	</div>
</template>

<script>
	export default {
		name:"cactus",
		data() {
			return {
				cactus:new Object(),
			}
		},
		created() {this.init();},
		methods: {
			init(){
				for (let i = 0; i < 3; i++) {
					this.cactus.[i]=this.createCactus(i*2.5);
				}
			},
			createCactus(num){
				let createRandom=this.createRandom;
				let style = createRandom(1, 5);
				let height = createRandom(40, 60);
				let width = height/5;
				let s1 ="height:"+height+"%;width:"+width+"%;animation-delay:"+num+"s;";
				let s2 ="c"+style;
				return {style:s1,class:s2,}
			},
			createRandom(start, end) {//生成随机数
				let abs = Math.abs(start - end);
				return Math.round(start + Math.round(Math.random() * abs));
			}
		}
	}
</script>

<style lang="less">
	.cactus {
		position: absolute;
		top: -30vh;
		left: 0;
		width: 100%;
		height: 30vh;
		z-index: -1;
	}
	.cactus span {
		display: block;
		position: absolute;
		bottom: -5%;
		left: 100%;
		width: 10%;
		height: 50%;
		background: url(../assets/cactus/c1.svg);
		background-size: contain;
		background-repeat: no-repeat;
		background-position: center;
		z-index: 1;
		animation: move 6s linear infinite both;
		&.c1 {background-image: url(../assets/cactus/c1.svg)}
		&.c2 {background-image: url(../assets/cactus/c2.svg)}
		&.c3 {background-image: url(../assets/cactus/c3.svg)}
		&.c4 {background-image: url(../assets/cactus/c4.svg)}
		&.c5 {background-image: url(../assets/cactus/c5.svg)}
	}

	@keyframes move {
		0% {
			left: 100%;
		}

		100% {
			left: -10%;
		}
	}
</style>
