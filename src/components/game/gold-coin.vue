<template>
	<div class="gold-coins">
		<span v-for="x in golds" :x="x.x" :y="x.y" :style="x.s" :key="x.index"></span>
	</div>
</template>

<script>
	export default {
		name:"gold",
		data(){
			return{
				golds:[],//金币组
				index:1,//初始索引
				total:1,//屏幕中能出现的金币数量上限
				style:"",//样式
				x:100,y:0,//金币坐标
				dinosaur:null,//保存小恐龙组件索引
			}
		},
		computed:{
			exactyX:{get(){return this.x*window.innerWidth*0.01}},
		},
		mounted(){
			let game=this.$parent;
			let difficulty=game.$parent.difficulty;
			this.dinosaur=this.$parent.get("dinosaur");
			if(difficulty=="normal") this.total=2;
			if(difficulty=="hard") this.total=4;
			if(difficulty=="hell") this.total=5;
			for (var i = 0; i < this.total; i++) {
				setTimeout(this.add,i*1000);
			}
		},
		methods:{
			//生成随机数-整数
			createRandom(start, end) {
				let abs = Math.abs(start - end);
				return Math.round(start + Math.round(Math.random() * abs));
			},
			//新增-生成金币
			add(){
				if(this.golds.length>=this.total) return;
				let width=Math.round(40/(window.innerWidth/100));
				//-----增加生成判定，禁止与仙人掌重合------
				let game=this.$parent;
				let c=game.get("cactus");
				if(!c) return;
				let cArr=c.cactus;
				for(let item of cArr){//判断是否与仙人掌重合
					let cx=item.x+width;//右边
					if(cx>=100){
						setTimeout(this.add,100);
						return;
					}
				}
				for(let item of this.golds){//判断是否与其它金币重合
					let x=item.x;
					if(x>=(100-(width*1.1))){
						setTimeout(this.add,100);
						return;
					}
				}
				//---------------重合判定 结束---------------
				this.golds.push({
					index:this.index++,
					s:`left:100%;bottom:${this.height}`,
					width:width,
					height:width,
					x:100,y:0,
				});
			},
			//检查
			check(){
				let game=this.$parent;
				let moveSpeed=game.moveSpeed;
				let timeAmend=game.speedTimeAmend;
				let s=game.$parent.totalSeconds;
				moveSpeed+=parseInt(s/game.speedIncreaseLag)*timeAmend;//时间加速
				for (let i = 0; i < this.golds.length; i++) {
					let g=this.golds[i];
					this.x=g.x;
					this.x-=moveSpeed;
					g.x=this.x;
					g.s = `left:${this.exactyX}px;`;
					if(this.x<-g.width)//出屏销毁/复原
						g.x=100;
					//碰撞
					let golds=this.golds;//仙人掌
					let d=this.dinosaur;//小恐龙
					let dLeft=d.x;
					let dRight=d.x+d.size-2;
					let dBottom=d.y+1.5;
					for (let i = 0; i < golds.length; i++) {
						let g=golds[i];
						let crashAmend=1;
						let gLeft=g.x+crashAmend;			let gRight=g.x+g.width-crashAmend*2;
						let gTop=g.y+g.height;
						//碰撞
						if(dRight>=gLeft&&dLeft<=gRight&&dBottom<=gTop){
							game.$parent.goldCoins++;
							game.eatGolds+=1;
							this.getGold();
							//销毁
							golds.splice(i,1);
							setTimeout(this.add,this.createRandom(5,30)*100);
						}
					}
				}
			},
			getGold(){
				//获得金币音效
				this.$parent.play("gold",2,8.08,0.1,true);
				//小恐龙获取金币提示
				this.dinosaur.getGold();
			},
			panel(){return this.$parent.$parent},//返回panel组件实例
		}
	}
</script>

<style lang="less" scoped>
	.gold-coins{
		position: absolute;
		bottom: 40%;left: 0;
		width: 100%;height: 30vh;
		overflow: hidden;
		z-index: 1;
	}
	.gold-coins span{
		display: block;
		position: absolute;
		bottom: 4%;left: 100%;
		--size:40px;
		width: var(--size);height: var(--size);
		background: radial-gradient(#ffdb0b 0 60%,#ffaa00 60%);
		border-radius: 50%;
		&::before{
		content: '¥';
		display: flex;
		justify-content: center;
		align-items: center;
		position: absolute;
		left: 0;top: 0;
		width: 100%;height: 100%;
		color: #ffaa00;
		font-size: 120%;
		}
	}
</style>
