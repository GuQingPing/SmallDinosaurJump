<template>
	<transition name="fade">
	<div id="goodPanel" v-show="panelShow">
		<p>名称 {{name}}</p>
		<p>价格 {{price}} ¥</p>
		<p>描述 {{describe}}</p>
		<div class="btnBox">
			<div v-if="!owned" class="btn buy" @click="buy()">购买</div>
			<div v-if="owned" class="btn use" @click="use()">使用</div>
			<div class="btn exit" @click="close()">关闭</div>
		</div>
	</div>
	</transition>
</template>

<script>
	export default {
		name:'goodPanel',
		data(){
			return{
				id:101,
				panelShow:false,
				name:null,
				price:null,
				describe:null,
				owned:null,
			}
		},
		methods:{
			open(x){//显示
				this.panelShow=true;
				this.id=x.id.toString();
				this.name=x.name;
				this.price=x.price;
				this.describe=x.describe;
				this.owned=x.owned;
			},
			close(){//关闭
				this.panelShow=false;
			},
			buy(){//购买
				let shop=this.$parent.$parent;
				let coins=shop.$parent.goldCoins;
				let goods=shop.goods;
				let price=this.price;
				// coins+=10000;
				if(coins>=price){
					coins-=price;
					let x=this.id.substring(0,1);
					let y=this.id.substring(2);
					let good=goods[x-1][y-1];
					console.log(good.name+" owned");
					this.owned=good.owned=true;
					shop.saveGoods();
				}
			},
			use(){//使用
				
			},
		}
	}
</script>

<style lang="less" scoped>
	#goodPanel{
		position: absolute;
		--mar:10%;
		left: var(--mar);bottom: var(--mar);
		right: var(--mar);top: var(--mar);
		z-index: 2;
		box-sizing: border-box;
		padding: 2% 4%;
		background: linear-gradient(to right bottom,rgb(104, 145, 203),rgb(115, 156, 222) 20% 80%,rgb(104, 145, 203));
		color: rgb(229, 228, 234);
		.btnBox{
			position: absolute;
			bottom: 2%;left: 0;
			.btn{
				cursor: pointer;
				text-align: center;
				background: rgb(228, 71, 56);
				&.buy{
					background: rgb(110, 142, 78);
				}
				&.use{
					background: rgb(239,176,65);
				}
			}
		}
	}
</style>
