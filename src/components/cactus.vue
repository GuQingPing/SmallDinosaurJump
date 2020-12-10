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
				cactus:[],
				cId:1,
				creator:null,
				lag:6,
			}
		},
		created() {
			this.createCactus();
			this.creator=setTimeout(this.add,this.lag*1000);
		},
		methods: {
			createCactus(){
				let difficulty=this.$parent.$parent.difficulty.toLowerCase();
				let createRandom=this.createRandom;
				let style = createRandom(1, 5);
				let height = createRandom(30, 60);
				let width = height/5;
				let s1 =`id:${this.cId++};height:${height}%;width:${width}%;`;
				let s2 =`c${style}`;
				this.cactus.push({style:s1,class:s2});
			},
			add(){
				let difficulty=this.$parent.$parent.difficulty.toLowerCase();
				let cTotal=7;
				if(difficulty=="easy") cTotal=5;
				if(difficulty=="hard") cTotal=9;
				if(difficulty=="hell") cTotal=12;
				if(this.cactus.length>=cTotal) this.cactus.shift();
				this.createCactus();
				let lagAmend=1;
				if(difficulty=="easy") lagAmend=1.5;
				if(difficulty=="hard") lagAmend=0.5;
				if(difficulty=="hell") lagAmend=0.2;
				this.lag=this.createRandom(lagAmend*4,lagAmend*8);
				setTimeout(this.add,this.lag*1000);
			},
			createRandom(start, end) {//生成随机数
				let abs = Math.abs(start - end);
				return Math.round(start + Math.round(Math.random() * abs));
			},
		}
	}
</script>

<style lang="less" scoped>
	.cactus {
		position: absolute;
		top: -30vh;
		left: 0;
		width: 100%;
		height: 30vh;
		z-index: -1;
	}
	.cactus span{
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
		0% {left: 100%}
		100% {left: -10%}
	}
</style>
