<template>
	<transition name="fade">
	<div id="skinPanel" v-show="this.$parent.skinPanel">
		<dinosaur :showMode="true"></dinosaur>
		<div id="selectPanel">
			<div class="typeBox">
				<span v-for="x in 10" class="btn type">文本</span>
				<div class="type2">
					<span v-for="x in 10" class="btn type">二级文本</span>
				</div>
			</div>
			<div class="content">
				
			</div>
		</div>
	</div>
	</transition>
</template>

<script>
	/**
	 * 竖排菜单
	 * 菜单中间栏局部部件皮肤(手部头部躯体)
	 * 右下角动作按钮
	 */
	import dinosaur from '../game/dinosaur.vue'
	export default {
		name:'skinPanel',
		components:{dinosaur},
		data(){
			return{
				type:["皮肤","拖尾","曳光","颜色","重坠","死亡","碰撞","地图"],
				color:["肤色","瞳色","拖尾色","曳光色","烟雾色"],
			}
		},
		methods:{
			open(x){//显示
				this.$parent.skinPanel=true;
			},
			close(){//关闭
				this.$parent.skinPanel=false;
			},
		}
	}
</script>
<style lang="less">
	#skinPanel{
		#dinosaur{
			left: 85%;
			transform: scale(2);
			box-shadow: 0px 0px 0px 100px rgba(255,255,255,1) inset,
						2px 0px 0px 5px rgba(255,255,255,1);
		}
	}	
</style>
<style lang="less" scoped>
	#skinPanel{
		position: absolute;
		--mar:10%;
		left: var(--mar);bottom: var(--mar);
		right: var(--mar);top: var(--mar);
		z-index: 2;
		box-sizing: border-box;
		padding: 2% 4%;
		background: linear-gradient(to right bottom,rgb(217, 217, 217),rgb(203, 203, 203) 20% 80%,rgb(217, 217, 217));
		color: rgb(229, 228, 234);
		border: .2vw solid #b0b0b0;
		#selectPanel{
			color: #000;
			.typeBox{
				width: 50%;
				background: red;
			}
		}
	}
</style>
