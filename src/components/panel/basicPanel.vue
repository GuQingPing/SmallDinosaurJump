<template>
	<transition :name="transition">
		<div class="bg" v-show="show">
			<div :class="classList">
				<slot></slot>
				<div class="btnBox">
					<div v-if="type==1" class=" btn close" @mousedown="close">关闭</div>
					<div v-if="showConfirm" class=" btn close green" @mousedown="close">确认</div>
					<div v-if="type==3" class=" btn close" @mousedown="close">取消</div>
				</div>
			</div>
		</div>
	</transition>
</template>
<script>
	/*
		Type参数说明
		1关闭按钮
		2确认按钮
		3确认，取消按钮
		4警告图标，确认按钮
	*/
	export default {
		name:'basicPanel',
		data(){
			return{
				classList:"allPanel",
				transition:"fade",
			}
		},
		mounted() {//调整参数
			if(this.fullScreen=="true"){
				this.classList="allPanel full";
				this.transition="fade";
			}
		},
		computed:{
			showConfirm(){
				let type=this.type;
				return type==2|type==3|type==4;
			}
		},
		props:{
			type:{default:2},
			show:{default:false},
			fullScreen:{default:false},
			close:{
				default:function(){
					this.show=false;
				}
			}
		},
		methods:{
			get(name){//获取
				for (let x of this.$children) {
					if(x.$options.name==name) return x;
				}
				return null;
			},
		}
	}
</script>

<style lang="less">
	.bg{
		position: fixed;
		top: 0%;left: 0%;
		width: 100%;height: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
		z-index: 10;
		background: rgba(0,0,0,.2);
	}
	.allPanel{
		min-width: 740px;
		min-height: 300px;
		font-size: 1.7rem;
		position: absolute;
		border-radius: 15px;
		background: rgba(199, 199, 199,.9);
		backdrop-filter: blur(2px);
		padding: 1em 2em 3em 2em;
		box-sizing: border-box;
		text-align: left;
		&.full{//全屏
			width: inherit;height: inherit;
			padding: 0;
		}
		.btn{
			cursor: pointer;
			user-select: none;
			border-radius: 5px;
			display: inline-block;
			background: #b4b4b4;
			padding: .25em 1em;
			transition: .25s;
			&:hover{
				filter: brightness(103%);
				transform: scale(1.1);
				z-index: 10;
			}
		}
		.btnBox{
			position: absolute;
			bottom: 0;left: 0;
			width: 100%;
			display: flex;
			justify-content: space-around;
			padding-bottom: 1.5%;
		}
		.close{
			user-select: none;
			--c1:red;--c2:white;
			display: block;
			background: var(--c1);
			padding: .25em 2em;
			border-radius: 5px;
			color: var(--c2);
		}
		.green{
			--c1: rgba(28, 213, 172, 1.0);
		}
	}
	.zoom-enter-active{//动画激活
	  animation: scale reverse .3s both,radius reverse .3s -100ms both;
	}
	.zoom-leave-active{//动画失效
	  animation: scale .3s both,radius .3s both;
	}
	.fade-enter-active{
	  animation: fade reverse .3s both;
	}
	.fade-leave-active{
	  animation: fade 0s both;
	}
</style>
