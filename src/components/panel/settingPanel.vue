<template>
	<panel type="0" :show="this.$parent.settingPanel" class="settingPanel" :close="close">
		<div class="cBox">
			<div style="margin-bottom: .3em;" @click="toggle('click')">
				<span style="margin-right: 15em;">点击提示音</span>
				<div class="circleBtn clicked" toggle="click" ref="clickSwitch"><span></span></div>
			</div>
			<div style="margin-bottom: .4em;" @click="toggle('hover')">
				<span style="margin-right: 13em;">鼠标经过提示音</span>
				<div class="circleBtn clicked" toggle="hover" ref="hoverSwitch"><span></span></div>
			</div>
		</div>
		<p style="margin-bottom: .3em;">主音量<input type="range" @input="setV('inputVolume',$event)" :value="this.$parent.inputVolume">{{this.$parent.inputVolume}}</p>
		<p style="margin-bottom: .3em;">音乐<no>一</no><input type="range" @input="setV('inputMusic',$event)" :value="this.$parent.inputMusic">{{this.$parent.inputMusic}}</p>
		<p>音效<no>一</no><input type="range" @input="setV('inputSound',$event)" :value="this.$parent.inputSound">{{this.$parent.inputSound}}</p>
		<p v-if="this.$parent.visible" class="dBox">
			<label v-for="x in dcList" :for="x.id" class="btn">
				{{x.text}}
				<input class="btn" type="radio" name="difficulty" :value="x.value" :id="x.id" @input="setV('difficulty',$event)" :checked="isChecked(x.value)">
				<span></span>
			</label>
		</p>
		<p v-if="!(this.$parent.visible)" style="text-transform:capitalize">当前难度: {{this.$parent.difficulty}}</p>
		<div class="btnBox">
			<div class="btn back" v-if="!(this.$parent.visible)" @click="back">返回主界面</div>
			<div @click="close" class="btn close">关闭</div>
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
			back(){//返回主界面
				this.$parent.visible=true;
				this.$parent.settingPanel=false;
				let game=this.$parent.get("game");
				clearInterval(game.checker);
			},
			setV(p,e){//设置值
				this.$parent.set(p,e.target.value);
				this.$parent.setStorage();
			},
			close(){//关闭面板
				this.$parent.set("settingPanel",false);
			},
			isChecked(v){//判断难度是否被选中
				return this.$parent.difficulty==v;
			},
			toggle(v){//切换音效开关
				let list=this.$refs.[`${v}`+"Switch"].classList;
				if(list.contains("clicked")){
					//关闭音效
					list.remove("clicked");
					this.$parent.soundSwitch.[`${v}`]=false;
				}else{
					//开启音效
					list.add("clicked");
					this.$parent.soundSwitch.[`${v}`]=true;
				}
				console.log(v+"="+list.contains("clicked"));
				this.$parent.setStorage();
			},
			init(){//初始化样式
				let s=this.$parent.soundSwitch;//获取当前配置
				let s1=this.$refs.clickSwitch.classList;
				let s2=this.$refs.hoverSwitch.classList;
				console.log("已获取缓存配置");
				if(s.click) s1.add("clicked");
				else s1.remove("clicked");
				if(s.hover) s2.add("clicked");
				else s2.remove("clicked");
			}
		},
		mounted(){
			this.init();
		},
		components:{
			no,panel
		}
	}
</script>

<style lang="less" scoped="scoped">
	.settingPanel{
		p,.cBox{
			margin: 1% 0;
		}
		.cBox{
			display: block;
			width: 100%;
			.circleBtn{
				--bgColor:rgb(156, 157, 156);
				display: inline-block;
				position: relative;
				width: 8%;
				background: var(--bgColor);
				border-radius: 100px;
				border: 2px solid var(--bgColor);
				height: 18px;
				box-shadow: 0px 0px 8px rgba(0,0,0,.1);
				transition: .3s;
				cursor: pointer;
				span{
					left: -5%;top: -23%;
					display: block;
					position: absolute;
					width: 25px;height: 25px;
					border-radius: 50%;
					background: #eeeeee;
					border: 1px solid var(--bgColor);
					transition: .3s;
					box-shadow: 2px 2px 5px rgba(0,0,0,.2);
				}
				&.clicked{
					--bgColor:rgb(4, 211, 4);
					span{left: 48%;}
				}
			}
		}
		.dBox{
			margin-top: .4em;
			display: flex;
			align-items: center;
			justify-content: center;
		}
		label.btn{
			font-size: 70%;
			padding: .2em 1.6em;
			border-radius: 5px;
			color: white;
			margin-right: .8em;
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
