<template>
	<div class="cactus">
		<span v-for="x in cactus" :x="x.x" :y="x.y" :class="x.c" :style="x.s" :key="x.index"></span>
	</div>
</template>

<script>
	export default {
		name: "cactus",
		data(){
			return{
				cactus:[],//仙人掌组
				index:1,//初始索引
				total:2,//屏幕中能出现的仙人掌数量上限
				x:100,y:0,//仙人掌坐标
				c:"",s:"",//类与样式
				height:0,//高度
				minLag:10,//最低仙人掌间隔
				maxLag:30,//最高仙人掌间隔
				minCLag:3,//仙人掌间隔倍数
				reduceHealth:1,//扣除生命值
				dinosaur:null,//保存小恐龙组件索引
			}
		},
		computed:{
			exactyX:{get(){return this.x*window.innerWidth*0.01}},
		},
		mounted() {//初始化数据(根据难度调整)
			let game=this.$parent;
			let difficulty=game.$parent.difficulty;
			this.dinosaur=this.$parent.get("dinosaur");
			if(difficulty=="normal"){
				this.minLag=10;
				this.maxLag=12;
				this.total=3;
			}
			if(difficulty=="hard"){
				this.maxLag=9;
				this.minLag=7;
				this.total=4;
			}
			if(difficulty=="hell"){
				this.minLag=5;
				this.maxLag=7;
				this.total=5;
			}
			let minLag=this.minLag;
			let maxLag=this.maxLag;
			for (var i = 0; i < this.total; i++) {
				setTimeout(this.add,this.createRandom(minLag,maxLag)*100);
				minLag+=minLag;
				maxLag+=maxLag;
			}
			if(difficulty=="easy") game.maxHealthy=5;
			if(difficulty=="normal"){
				game.moveSpeed=.23;//初始移速
				game.timeAmend=.04;//时间加速
				this.minCLag=2.5;//仙人掌间隔倍数
				game.maxHealthy=4;//小恐龙最大生命值
			}
			if(difficulty=="hard"){
				game.moveSpeed=.26;
				game.timeAmend=.035;
				this.minCLag=1.8;
				game.maxHealthy=2;
			}
			if(difficulty=="hell"){
				game.moveSpeed=.3;
				game.timeAmend=.02;
				this.minCLag=1;
				game.maxHealthy=1;
			}
		},
		methods:{
			//生成随机数-整数
			createRandom(start, end) {
				let abs = Math.abs(start - end);
				return Math.round(start + Math.round(Math.random() * abs));
			},
			//新增-生成仙人掌
			add(){
				if(this.cactus.length>=this.total) return;
				let style = this.createRandom(1, 4);
				let height = this.createRandom(40, 60);
				//-----增加生成判定，禁止重合------
				for(let item of this.cactus){
					let x=item.x;
					if(x>100-height/9*2*this.minCLag){
						setTimeout(this.add,100);
						return;
					}
				}
				this.cactus.push({
					c:"c"+style,
					height:height,
					width:height/9,
					index:this.index++,
					s:`height:${height}%;width:${height/9}%;left:100%`,
					x:100
				});
				//---------------重合判定 结束---------------
			},
			//检查
			check(){
				let game=this.$parent;
				let moveSpeed=game.moveSpeed;
				let timeAmend=game.speedTimeAmend;
				let s=this.$parent.$parent.totalSeconds;
				moveSpeed+=parseInt(s/game.speedIncreaseLag)*timeAmend;//时间加速
				//移动与销毁判定
				for (let i = 0; i < this.cactus.length; i++) {
					let c=this.cactus[i];
					this.x=c.x;
					this.x-=moveSpeed;
					c.x=this.x;
					let width = c.height / 9;
					c.s = `height:${c.height}%;width:${width}%;left:${this.exactyX}px;`;
					if(this.x<-width){//出屏销毁
						this.cactus.splice(i,1);
						setTimeout(this.add,this.createRandom(this.minLag,this.maxLag)*100);
					}
				}
				//碰撞
				let cactus=this.cactus;//仙人掌组
				let d=this.dinosaur;//小恐龙
				let panel=d.panel();
				let dLeft=d.x;
				let dRight=d.x+d.size-2;
				let dBottom=d.y+1.5;
				for (let i = 0; i < cactus.length; i++) {
					let c=cactus[i];
					let crashAmend=2;//碰撞修正
					let cLeft=c.x+crashAmend;			let cRight=c.x+c.width-crashAmend*2;
					let cTop=c.height*0.28;
					
					//判断碰撞
					if(dRight>=cLeft&&dLeft<=cRight&&dBottom<=cTop){
						if(panel.model!="run"||d.failed) return;//如果失败或者debug模式就不判定碰撞了
						if(game.inCrash) return;
						game.inCrash=true;
						setTimeout(()=>{
							game.inCrash=false;
						},300);
						game.healthy-=this.reduceHealth;					//扣除生命值
						if(game.healthy<0) game.healthy=0;					//防止负数生命值
						game.play("falled",2);								//播放碰撞音效
						game.bgClass="paused";
						setTimeout(()=>{
							game.bgClass=game.healthy<=0?"paused":"running";
						},200);
						console.log("Now Health:"+game.healthy);
						if(game.healthy>0) return;							//若生命值不够扣，再判定失败
						console.log("游戏失败");
						clearInterval(game.checker);						//清除游戏进程(定时器)
						let score=panel.score;								//获取分数
						game.play("failed");								//播放失败音效
						let heighest=localStorage.getItem("record")??0;		//获取历史最高分
						if(score>heighest){
							localStorage.setItem("record",score);			//缓存当前分数
							panel.record=panel.score;						//同步分数
						}
						d.cName="dead";										//切换失败样式
						game.failed=d.failed=true;							//设置游戏失败
						panel.failedPanel=true;								//显示失败面板
						localStorage.setItem("gold-coins",panel.goldCoins);	//缓存当前金币数
					}
				}
			},
			panel(){return this.$parent.$parent},//返回panel组件实例
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
