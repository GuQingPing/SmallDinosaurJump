<template>
	<div class="cactus">
		<span v-for="x in cactus" :x="x.x" :y="x.y" :class="x.c" :style="x.s"></span>
	</div>
</template>

<script>
	export default {
		name: "cactus",
		data(){
			return{
				cactus:[],//仙人掌组
				index:1,//初始索引
				total:3,//屏幕中能出现的仙人掌数量上限
				x:100,y:0,//仙人掌坐标
				c:"",s:"",//类与样式
				height:0,//高度
			}
		},
		computed:{
			exactyX:{get(){return this.x*window.innerWidth*0.01}},
		},
		methods:{
			//生成随机数-整数
			createRandom(start, end) {
				let abs = Math.abs(start - end);
				return Math.round(start + Math.round(Math.random() * abs));
			},
			//新增
			add(){
				let difficulty=this.$parent.$parent.difficulty;
				if(difficulty=="normal") this.total=4;
				if(difficulty=="hard") this.total=5;
				if(difficulty=="hell") this.total=6;
				if(this.cactus.length>this.total) return;
				let style = this.createRandom(1, 4);
				let height = this.createRandom(40, 60);
				this.cactus.push({
					c:"c"+style,
					height:height,
					width:height/9,
					index:this.index++,
					s:`height:${height}%;width:${height/9}%;left:100%`,
					x:100
				});
				setTimeout(this.add,this.createRandom(500,5000));
			},
			//检查
			check(){
				let moveSpeed=.2;
				let difficulty=this.$parent.$parent.difficulty;
				if(difficulty=="normal") moveSpeed=.23;
				if(difficulty=="hard") moveSpeed=.26;
				if(difficulty=="hell") moveSpeed=.3;
				for (let i = 0; i < this.cactus.length; i++) {
					let c=this.cactus[i];
					this.x=c.x;
					this.x-=moveSpeed;
					c.x=this.x;
					let width = c.height / 9;
					if(this.x<-width){//刷新属性
						this.x=c.x=100;
						let style = this.createRandom(1, 4);
						c.height = this.createRandom(40, 70);
						c.c = `c${style}`;
					}
					c.s = `height:${c.height}%;width:${width}%;left:${this.exactyX}px;`;
				}
			}
		}
	}
</script>

<style lang="less" scoped>
	.cactus {
		position: absolute;
		bottom: 40%;left: 0;
		width: 100%;height: 30vh;
		overflow: hidden;
		z-index: 1;
	}
	.cactus span {
		display: block;
		position: absolute;
		bottom: -1%;
		left: 100%;
		width: 5%;height: 50%;
		background: url(../../assets/cactus/c1.svg);
		background-size: contain;
		background-repeat: no-repeat;
		background-position: bottom center;
		@pos:95%;
		clip-path: polygon(0 0, 100% 0, 100% @pos, 0 @pos);
		&.c2 {background-image: url(../../assets/cactus/c2.svg)}
		&.c3 {background-image: url(../../assets/cactus/c3.svg)}
		&.c4 {background-image: url(../../assets/cactus/c4.svg)}
	}
</style>
