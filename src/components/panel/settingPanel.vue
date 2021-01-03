<template>
	<panel type="0" :show="this.$parent.settingPanel" class="settingPanel" :close="close">
		<p>主音量<input type="range" @input="setV('inputVolume',$event)" :value="this.$parent.inputVolume">{{this.$parent.inputVolume}}</p>
		<p>音乐<no>啊</no><input type="range" @input="setV('inputMusic',$event)" :value="this.$parent.inputMusic">{{this.$parent.inputMusic}}</p>
		<p>音效<no>啊</no><input type="range" @input="setV('inputSound',$event)" :value="this.$parent.inputSound">{{this.$parent.inputSound}}</p>
		<p>点击提示音<div></div></p>
		<p>经过提示音</p>
		<p v-if="this.$parent.visible">难度
			<label v-for="x in dcList" :for="x.id" class="btn">
				{{x.text}}
				<input class="btn" type="radio" name="difficulty" :value="x.value" :id="x.id" @input="setV('difficulty',$event)" :checked="isChecked(x.value)">
				<span></span>
			</label>
		</p>
		<p v-if="!(this.$parent.visible)" style="text-transform:capitalize">当前难度: {{this.$parent.difficulty}}</p>
		<div class="btnBox">
			<div class="btn back" v-if="!(this.$parent.visible)" @click="back">返回主界面</div>
			<div @click="close" class=" btn close">关闭</div>
		</div>
	</panel>
</template>

<script>
	import no from '../others/no.vue';
	import panel from './basicPanel.vue'
	export default {
		data(){
			return{
				dcList:[				//难度按钮列表
					{id:"dc1",text:"简单",value:"easy"},
					{id:"dc2",text:"普通",value:"normal"},
					{id:"dc3",text:"困难",value:"hard"},
					{id:"dc4",text:"炼狱",value:"hell"},
				],
			}
		},
		methods:{
			back(){this.$parent.visible=true;this.$parent.settingPanel=false},
			setV(p,e){this.$parent.set(p,e.target.value)},
			close(){this.$parent.set("settingPanel",false);},
			isChecked(v){
				return this.$parent.difficulty==v;
			}
		},
		components:{
			no,panel
		}
	}
</script>

<style lang="less" scoped="scoped">
	.settingPanel{
		.no{opacity: 0;}
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
			input+span{
				position: absolute;
				border-radius:inherit;
				top: 0;bottom: 0;left: 0;right: 0;
				filter: brightness(50%);
				transition: .2s;
			}
			input:checked+span{
				@bx:3px;
				box-shadow: @bx -@bx 0px var(--color),-@bx @bx 0px var(--color);
			}
		}
		input[type="range"]{width: 75%;}
		.back{padding: .25em 5em;}
		.close{padding: .25em 2.5em;}
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
