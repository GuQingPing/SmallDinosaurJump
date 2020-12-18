<template>
	<div v-if="settingPanel" class="allPanel settingPanel">
		<p>音量<input type="range" v-model="localInputVolume" @input="setVolume">{{localInputVolume}}</p>
		<p v-if="visible">难度
			<label v-for="x in dcList" :for="x.id" class="btn">
				{{x.text}}
				<input v-model="localDifficulty" class="btn" type="radio" name="difficulty" :value="x.value" :id="x.id" @input="setDifficulty">
				<span></span>
			</label>
		</p>
		<p v-if="!visible" style="text-transform:capitalize">当前难度: {{localDifficulty}}</p>
		<div class="btn back" v-if="!visible" @click="back">返回主界面</div>
		<div @click="close" class=" btn close">关闭</div>
	</div>
</template>

<script>
	export default {
		data(){
			return{
				localDifficulty:'',
				localInputVolume:0,
			}
		},
		mounted() {
			this.localDifficulty=this.$parent.difficulty;
			this.localInputVolume=this.$parent.inputVolume;
		},
		props:{
			visible:{},
			settingPanel:{},
			dcList:{},
		},
		methods:{
			setVolume(e){
				let nV=e.currentTarget.value;
				this.localInputVolume=nV;
				this.$parent.inputVolume=e.currentTarget.value;
			},
			setDifficulty(e){
				let nV=e.currentTarget.value;
				this.localDifficulty=nV;
				this.$parent.difficulty=nV;
			},
			back(){this.$parent.visible=true;this.$parent.settingPanel=false},
			close(){this.$parent.settingPanel=false},
		}
	}
</script>

<style lang="less" scoped="scoped">
	.settingPanel{
		p label.btn{
			cursor: pointer;
			font-size: 70%;
			padding: 0 1.6em;
			border-radius: 5px;
			color: white;
			margin-left: .8em;
			--color:limegreen;
			position: relative;
			background:var(--color);
			input{display: none;}
			@size:1px;
			&:hover{box-shadow: @size -@size 0px #000,-@size @size 0px #000;}
			&:nth-child(2){--color:grey;}
			&:nth-child(3){--color:orange;}
			&:nth-child(4){--color:red;}
			&:nth-child(5){--color:#000;}
			& input+span{
				position: absolute;
				border-radius:inherit;
				top: 0;bottom: 0;left: 0;right: 0;
				filter: brightness(50%);
				transition: .2s;
			}
			& input:checked+span{
				@bx:3px;
				box-shadow: @bx -@bx 0px var(--color),-@bx @bx 0px var(--color);
			}
		}
		input[type="range"]{width: 75%;}
		.back{
			position: absolute;
			bottom: 5%;left: 5%;
			padding: .25em 5em;
		}
		.close{
			left: 80%;
		}
	}
	input[type="range"]{
		appearance: none;
		background: #6d6d6d;
		margin: 0 .5em;
		width: 16em;
		height: 20px;
		border: none;
		border-radius: 5px;
		transition: .3s;
	}
	input[type="range"]:hover{background: #585858;}
	input[type="range"]::-webkit-slider-thumb {
		appearance: none;
		@size:30px;
		width: @size/3;height: @size;
		background: #dfdfdf;
		border-radius: 5px;
		box-shadow: 0px 0px 10px rgba(0,0,0,.2);
	}
</style>
