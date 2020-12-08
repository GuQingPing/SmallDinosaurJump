<template>
	<div class="dialog" v-if="visible">
		<div class="title">{{title}}</div>
		<div class="text">{{text}}</div>
		<div class="btn yes" @click="yes">确定</div>
		<div class="btn no" @click="no">取消</div>
	</div>
</template>

<script>
	export default {
		props:{
			title:{
				default:"请设置标题",
			},
			text:{
				default:"请设置文本请设置文本请设置文本请设置文本请设置文本请设置文本请设置文本请设置文本请设置文本请设置文本请设置文本请设置文本请设置文本",
			},
			type:{
				default:1,
			}
		},
		data(){
			return{
				visible:true,
			}
		},
		methods:{
			yes(){this.visible=false},
			no(){this.visible=false},
		},
		created(){
			
		}
	}
</script>

<style scoped lang="less">
	.dialog{
		position: fixed;
		left: calc(25% - 2em);top: 30%;
		border-radius: 15px;
		padding:1em;
		padding-bottom: 3em;
		background: linear-gradient(to right,#33bfff,rgb(70, 95, 255));
		width: 50%;height: 20vh;
		animation: scale-in 1s;
		z-index: 99;
		color: white;
		text-align: center;
		font-size: 120%;
		.title{
			font-size: 150%;
			margin-bottom: 1em;
		}
		.text{
			margin-bottom: 2em;
		}
		.btn{
			--c1:limegreen;
			--c2:white;
			margin: 0 1em;
			user-select: none;
			cursor: pointer;
			padding: .5em 2em;
			border-radius: 200px;
			display: inline-block;
			outline-offset: 140%;
			background: var(--c2);
			color: var(--c1);
			transition: .3s;
			&.no{
				--c1:red;
			}
			&:hover{
				background: var(--c1);
				color: var(--c2);
			}
		}
	}
	@keyframes scale-in{
		from{transform: scale(0);opacity: 0;}
		to{transform: scale(1);opacity: 1;}
	}
</style>
