<template>
	<div class="bg" v-if="show">
		<div class="allPanel">
			<slot></slot>
			<div class="btnBox">
				<div v-if="type==1" class=" btn close" @mousedown="close">关闭</div>
				<div v-if="showConfirm" class=" btn close green" @mousedown="close">确认</div>
				<div v-if="type==3" class=" btn close" @mousedown="close">取消</div>
			</div>
		</div>
	</div>
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
		computed:{
			showConfirm(){
				let type=this.type;
				return type==2|type==3|type==4;
			}
		},
		props:{
			type:{default:2},
			show:{default:false},
			close:{
				default:function(){
					this.show=false;
				}
			}
		}
	}
</script>

<style lang="less" scoped>
	.bg{
		position: fixed;
		top: 0;left: 0;
		width: 100%;height: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
		z-index: 10;
	}
	.allPanel{
		min-width: 740px;
		font-size: 1.7rem;
		position: absolute;
		border-radius: 15px;
		background: rgba(199, 199, 199,.9);
		backdrop-filter: blur(2px);
		padding: 1em 2em 3em 2em;
		line-height: 1.5em;
		box-sizing: border-box;
		text-align: left;
		animation: scale reverse .3s both;
		.btn{
			cursor: pointer;
			border-radius: 5px;
			display: inline-block;
			background: #b4b4b4;
			padding: .25em 1em;
			transition: .25s;
			&:hover{
				filter: brightness(90%);
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
</style>
