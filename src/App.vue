<template>
	<div id="app">
		<startUI></startUI>
		<div class="loading" :style="loadStyle">
			<div class="myicon">
				<div class="myicon-item item1"></div>
				<div class="myicon-item item2"></div>
				<div class="myicon-item item3"></div>
				<div class="myicon-item item4"></div>
				<div class="myicon-item item5"></div>
			</div>
		</div>
	</div>
</template>

<script>
	import startUI from './components/startUI.vue'
	export default {
		name: 'App',
		components: {
			startUI
		},
		data(){
			return{
				loadStyle:""
			}
		},
	}
</script>
<style lang="less">
	* {
		margin: 0;
		padding: 0;
		border: none;
	}
	*:focus{outline: none;}
	#app {
		width: 100%;
		height: 100vh;
		min-width: 900px;
		font-family: "微软雅黑", Avenir;
		overflow: hidden;
	}
	/* 进度条 */
	.loading {
		@bg:#313131;
		@color:#e5e5e5;
		color: @color;
		width: 100%;
		height: 100%;
		position: fixed;
		top: 0;
		left: 0;
		background: @bg;
		z-index: 10;
		.myicon {
			@size:3;
			width: @size*50px;
			height: @size*40px;
			position: absolute;
			top: 0;left: 0;bottom: 0;right: 0;
			margin: auto;
			.myicon-item {
				width: @size*5px;
				height: @size*10px;
				float: left;
				margin-right: @size*5px;
				animation: myicon 1s infinite;
				background:@color;
				&.item1 {animation-delay: -1s}
				&.item2 {animation-delay: -0.9s}
				&.item3 {animation-delay: -0.8s}
				&.item4 {animation-delay: -0.7s}
				&.item5 {animation-delay: -0.6s}
			}
		}
	}
	@keyframes myicon {
		0% 100%{transform: scaleY(1)}
		50% {transform: scaleY(3)}
	}
	@keyframes fade{
		0%{opacity: 1}
		50%{opacity: 1}
		100%{opacity: 0;visibility: hidden;}
	}
	@keyframes scale{
		from{transform: scale(1);}
		to{transform: scale(0);visibility: hidden;}
	}
	@keyframes topIn{
		0%{top: 100%;opacity: 0;}
		25%{top: 10%;opacity: 1;}
		75%{top: 10%;opacity: 1;}
		100%{top: -50%;opacity: 0;}
	}
	@keyframes radius{
		from{border-radius: 50%;}
		to{border-radius: 15px;}
	}
</style>
