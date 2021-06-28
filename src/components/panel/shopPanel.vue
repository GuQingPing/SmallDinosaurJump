<template>
	<panel type="0" fullScreen="true" :show="this.$parent.shopPanel" :close="close" id="shopPanel">
		<div id="typeBox" @click="click">
			<span v-for="(x,i) in type" class="btn type" :index="i" ref="types">{{x}}</span>
		</div>
		<div id="goodsBox">
			<span v-for="(x,i) in showGoods" class="goods" :style="s1+(i/20)+s2" :key="x.id" @mousedown="showGood(x)">
				<p>{{x.name}}</p>
				<p>{{x.price}}<span v-if="x.price==null">9999</span> ¥</p>
			</span>
		</div>
		<div class="nowGolds">
			{{goldCoins}}
			<span class="gold-coins"></span>
		</div>
		<span class="btn back" style="padding: 0;" @click="close">返回</span>
		<goodPanel></goodPanel>
	</panel>
</template>

<script>
	/**
	 * 补中间栏
	 * */
	import panel from './basicPanel.vue'
	import goodPanel from './goodPanel.vue'
	export default {
		name:"shopPanel",
		components:{panel,goodPanel},
		data(){
			return{
				s1:"animation-delay:",
				s2:"s;",
				id:1,
				type:['皮肤','拖尾','曳光','重坠','死亡','碰撞','地图'],
				goods:JSON.parse(localStorage.getItem("goods"))??
				[
					[//皮肤	
						{id:101,name:'躯体色',price:1000,describe:"解锁躯体的所有颜色使用权(不含眼睛)",owned:false},
						{id:102,name:'眼睛色',price:500,describe:"解锁眼睛的所有颜色使用权",owned:false},
						{id:103,name:'光',price:1200,describe:"解锁皮肤-光之行走",owned:false},
						{id:104,name:'影',price:1200,describe:"解锁皮肤-影之行走",owned:false},
						{id:105,name:'熔岩',price:1200,describe:"解锁皮肤-熔岩行者",owned:false},
						{id:106,name:'电',price:1200,describe:"解锁皮肤-熔岩行者",owned:false},
					],
					[//拖尾
						{id:201,name:'拖尾色',price:1000,describe:"解锁拖尾的所有颜色使用权",owned:false},
						{id:202,name:'残影',price:1200,describe:"解锁拖尾-残影",owned:false},
						{id:203,name:'鬼魅',price:1200,describe:"解锁拖尾-残影",owned:false},
					],
					[//曳光
						{id:301,name:'曳光色',price:1000,describe:"解锁重坠烟雾的所有颜色使用权",owned:false},
						{id:302,name:'强光',price:1200,describe:"解锁曳光-强光",owned:false},
						{id:303,name:'乱影',price:1200,describe:"解锁曳光-乱影",owned:false},
						{id:304,name:'电流',price:1200,describe:"解锁曳光-电流",owned:false},
					],
					[//重坠
						{id:401,name:'烟雾色',price:500,describe:"解锁重坠烟雾的所有颜色使用权",owned:false},
						{id:402,name:'爆炸',price:500,describe:"解锁重坠-爆炸",owned:false},
						{id:403,name:'电流',price:500,describe:"解锁重坠-电流",owned:false},
					],
					[//死亡
						{id:501,name:'解体',price:500,describe:"解锁死亡效果-解体",owned:false},
						{id:502,name:'数据',price:500,describe:"解锁死亡效果-数据",owned:false},
						{id:503,name:'光影',price:500,describe:"解锁死亡效果-光影",owned:false},
						{id:504,name:'飞升',price:500,describe:"解锁死亡效果-飞升",owned:false},
						{id:505,name:'圆寂',price:500,describe:"解锁死亡效果-圆寂",owned:false},
					],
					[//碰撞
						{id:601,name:'野蛮冲撞',price:500,describe:"解锁碰撞效果-解体",owned:false},
					],
					[//地图
						{id:701,name:'绿野仙踪',price:2000,describe:"解锁地图-绿野仙踪",owned:false},
						{id:702,name:'巨石嶙峋',price:2500,describe:"解锁地图-巨石嶙峋",owned:false},
						{id:703,name:'熔岩火山',price:3000,describe:"解锁地图-熔岩火山",owned:false},
					],
				],
				showGoods:[],
				goldCoins:localStorage.getItem("gold-coins") ?? 0,
			}
		},
		methods:{
			close(){this.$parent.set("shopPanel",false)},
			click(e){
				let c=e.target.classList;
				if(c!="") this.change(e.target.getAttribute('index'));
			},
			change(i){//切换栏目
				this.showGoods=this.goods[i];
				let types=this.$refs.types;
				if(!types) return;
				for (let j = 0; j < types.length; j++) {
					types[j].classList.remove("clicked");
				}
				types[i].classList.add("clicked");
				if(this.showGoods.length==0){
					for (let i = 0; i < 10;i++) {
						this.showGoods.push({id:this.id++,name:'暂无货物',price:'9999',describe:"该分类下暂无商品"},)
					}
				}
			},
			showGood(x){//显示商品面板
				let bp=this.get('basicPanel');
				let goodPanel=bp.get('goodPanel');
				goodPanel.open(x);
			},
			get(name){//获取
				for (let x of this.$children) {
					if(x.$options.name==name) return x;
				}
				return null;
			},
			saveGoods(){//保存商品数据
				let o=JSON.stringify(this.goods);
				localStorage.setItem("goods",o);
				console.log("商品更改已保存");
			},
		},
		mounted() {
			this.change(0);
		},
	}
</script>
<style lang="less" scoped>
	.bg{
		width: 100%;
		height: 100%;
		top: 0;left: 0;
	}
	#shopPanel{
		--bgColor:rgb(105, 141, 203);//整体背景色
		--bgColor2:rgb(115, 156, 222);//栏目背景色
		--bgColor3:rgb(105, 141, 203);//商品背景色
		--borderColor:rgb(229, 228, 234);//商品边框色
		--textColor:rgb(229, 228, 234);//栏目字体色
		--textColor2:rgb(229, 228, 234);//商品字体色
		--textColor3:rgb(239,176,65);//货币字体色
		min-width: 1295px;
		#typeBox{
			width: inherit;
			display: flex;
			justify-content: center;
			align-items: center;
			background: var(--bgColor2);
			.type{
				color: var(--textColor);
				background: linear-gradient(rgb(3, 13, 22),rgb(10, 46, 77));
				background: inherit;
				padding: .5% 3%;
				z-index: 1;
				position: relative;
				&::after{
					content: '';
					display: block;
					transform: scale(0);
					transition: .5s;
					background: linear-gradient(to right,rgba(229, 228, 234, 0.1),rgba(229, 228, 234,1),rgba(229, 228, 234,.1));
					width: 100%;
					height: 2px;
					position: absolute;
					left: 0;bottom: 5%;
					border-radius: 50%;
				}
				&:hover::after{
					transform: scale(.5,1);
				}
				&.clicked::after{
					transform: scale(1);
				}
			}
		}
		#goodsBox{
			background: var(--bgColor);
			box-sizing: border-box;
			padding: 2% 4%;
			height: 93.5%;
			.goods{
				user-select: none;
				color: var(--textColor2);
				display: inline-block;
				text-align: center;
				font-size: 60%;
				margin: 0% 1%;
				background: var(--bgColor3);
				border-radius: 5px;
				padding: .4%;
				border: 2px solid var(--borderColor);
				animation:fade .3s both reverse;
				cursor: pointer;
				.btn:first-child{margin-right: .5em;}
				transition: .25s;
				&:hover{
					transform: scale(1.1);
					filter: brightness(105%);
				}
			}
		}
		.nowGolds{
			position: absolute;
			bottom: 2%;left: 79%;
			width: 20%;
			display: flex;
			align-items: center;
			justify-content: flex-end;
			color:var(--textColor3);
		}
		.gold-coins{
			position: relative;
			display: inline-block;
			margin-left: 5px;
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
		.back{
			color: var(--textColor);
			border-radius: 0;
			position: absolute;
			background:transparent;
			left: 1%;top: 1%;
			border: 3px solid var(--textColor);
			transition: .3s;
			width: 4.5%;
			text-align: center;
			&:hover{
				transform: none;
				border: 3px solid transparent;
				background: var(--textColor);
				color: var(--bgColor2);
			}
		}
	}
</style>