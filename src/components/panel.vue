<template>
	<div id="panel" @click="click">
		<div v-if='model!="run"' class="debug">{{model}}</div>
		<div v-if="visible" class="ui">
			<div class="title"><span></span>小恐龙跳跃</div>
			<div class="btn start">开始游戏</div>
			<div class="btn shop">商城</div>
			<div class="btn achievement">成就</div>
			<div class="btn setting">设置</div>
			<div class="btn about">关于</div>
			<div class="version">
				<p>{{difficulty}}</p>
				<p>{{version}}</p>
			</div>
			<div class="record">{{record}}</div>
		</div>
		<div v-show="!visible" class="game">
			<div class="btnBox">
				<div class="btn sound" v-if="!visible">
					<div>
						<audio id="jump" src="../assets/sound/jump.mp3" @loadstart="setVolume"></audio>
						<audio id="bgm" src="../assets/sound/bgm.wav" autoplay loop></audio>
					</div>
				</div>
				<div class="btn setting"></div>
			</div>
			<div class="gameInfo">
				<div class="aliveTime">{{aliveTime}}</div>
				<div class="score">{{score}}</div>
				<div class="version">
					<p>{{difficulty}}</p>
				</div>
			</div>
			<bg></bg>
			<dinosaur v-if="!visible"></dinosaur>
		</div>
		<div v-if="settingPanel" class="allPanel settingPanel">
			<p>音量<input type="range" @input="setVolume" v-model="inputVolume">{{inputVolume}}</p>
			<p v-if="visible">难度
				<label v-for="x in dcList" :for="x.id" class="btn">
					{{x.text}}
					<input type="radio" name="difficulty" :value="x.value" :id="x.id" v-model="difficulty">
					<span></span>
				</label>
			</p>
			<p v-if="!visible" style="text-transform:capitalize">当前难度: {{difficulty}}</p>
			<div class="btn back" v-if="!visible" @click="visible=true;settingPanel=false">返回主界面</div>
			<div @click="settingPanel=false" class=" btn close">关闭</div>
		</div>
		<div v-if="failedPanel" class="allPanel failedPanel">
			<p>你碰到刺了!!!</p>
			<p>存活时间: {{aliveTime}}</p>
			<p>最终分数: {{score}}</p>
			<p style="text-transform: capitalize;">难度: {{difficulty}}</p>
			<div @click="visible=true;failedPanel=false" class="btn back">返回主菜单</div>
			<div @click="playAgain" class="btn again">重来</div>
			<div @click="failedPanel=false" class="btn close">关闭</div>
		</div>
		<div v-if="infoPanel" class="allPanel infoPanel">
			<span v-html="infoPanelText"></span>
			<div @click="infoPanel=false" class=" btn close">关闭</div>
		</div>
	</div>
</template>

<script>
	import dinosaur from './dinosaur.vue'
	import bg from './bg.vue'
	export default {
		name: "panel",
		data() {
			return {
				version: "betaV0.4.0",
				visible: true,
				aliveTime: "0:0:0:0",
				score: 0,
				inputVolume:80,
				difficulty:"normal",
				model:"run",
				settingPanel:false,
				failedPanel:false,
				infoPanel:false,
				infoPanelText:"未开发",
				dcList:[
					{id:"dc1",text:"简单",value:"easy"},
					{id:"dc2",text:"普通",value:"normal"},
					{id:"dc3",text:"困难",value:"hard"},
					{id:"dc4",text:"炼狱",value:"hell"},
				],
			}
		},
		watch:{
			inputVolume(nV,oV){
				localStorage.setItem("volume",nV);
			},
			difficulty(nV,oV){
				localStorage.setItem("difficulty",nV);
			},
			model(nV,oV){
				localStorage.setItem("model",nV);
			},
		},
		computed: {
			record: {
				get() {
					return "最高记录:" + localStorage.getItem("score") ?? 0;
				}
			},
			volume: {
				get() {
					return this.inputVolume/100;
				}
			},
		},
		created() { //页面加载完成之前(组件诞生)
			document.addEventListener('keydown', this.keyDown);
			this.getSetting();
		},
		mounted() { //页面加载完成后坚渐隐进度条
			this.$parent.$data.loadStyle = "animation:fade 1s both;"
		},
		destroyed() {
			document.removeEventListener('keydown', this.keyDown);
		},
		methods: {
			click(e) { //面板点击
				let c = e.target.classList;
				if (c.contains("start")) {//开始游戏
					this.visible = false;
				}
				if (c.contains("shop")) {//商城
					this.untapped();
				}
				if (c.contains("achievement")) {//成就
					this.untapped();
				}
				if (c.contains("setting")) {//设置
					this.settingPanel=!this.settingPanel;
				}
				if (c.contains("sound")) {//声音
				let sound=document.getElementsByClassName("sound")[0];
				let clicked=sound.classList.contains("clicked");
					this.inputVolume = clicked?100:0;
					this.setVolume();
				}
				if (c.contains("about")) {//关于
					this.infoPanelText=`
					<div style="padding-left:2.9em;padding-top:2vw;line-height:1.5em;">
					<p>作者: 古清平</p>
					<p style="text-transform:capitalize">当前难度: ${this.difficulty}</p>
					<p style="text-transform:capitalize">当前模式: ${this.model}</p>
					<p style="text-transform:capitalize">游戏版本: ${this.version}</p>
					<p>更新时间: 2020/12/10</p>
					</div>
					`;
					this.infoPanel=true;
				}
			},
			keyDown(e) {
				if (e.code == "Space") this.visible = false;
				if (e.code == "Slash"){
					this.model = this.model=="run"?"debug":"run";	
				}
			},
			setVolume(){
				if(this.visible) return;
				let sound=document.getElementsByClassName("sound")[0]
				let clicked=sound.classList.contains("clicked");
				let audio=sound.getElementsByTagName("audio");
				if(this.volume==0&&!clicked) sound.classList.add("clicked");
				if(this.volume>0&&clicked) sound.classList.remove("clicked");
				for (var i = 0; i < audio.length; i++) {
					audio[i].volume=this.volume;
				}
			},
			playAgain(){
				this.failedPanel=false;
				this.visible=true;
				setTimeout(()=>{
					this.visible=false;
				},50)
			},
			getSetting(){
				this.inputVolume=localStorage.getItem("volume")??this.inputVolume;
				this.difficulty=localStorage.getItem("difficulty")??this.difficulty;
				this.model=localStorage.getItem("model")??this.model;
			},
			untapped(){
				this.infoPanelText=`未开发`;
				this.infoPanel=true;
			}
		},
		components: {
			dinosaur,bg
		}
	}
</script>

<style scoped lang="less">
	@min:1366px;
	#panel {
		width: 100%;
		height: 100%;
		min-width: @min;
		.debug {
			position: relative;
			color: red;
			font-size: 150%;
			position: fixed;
			bottom: 7%;
			right: 2%;
			z-index: 2;
		}
		.version{text-transform:capitalize;}
		.ui {
			min-width: @min;
			background-size: contain;
			background: #e7e7e7;
			width: 100%;height: 100%;
			text-align: center;
			position: relative;
			box-sizing: border-box;
			user-select: none;
			padding-top: 7%;
			.title {
				background: url(../assets/img/dinosaur.png) no-repeat;
				background-position: calc(50% - 3em);
				background-size: contain;
				font-size: 300%;
				margin-bottom: 4%;
			}
			.btn {
				cursor: pointer;
				font-size: 200%;
				padding: .2em 0;
				width: 20%;
				margin: 0 auto;
				margin-bottom: .5em;
				border-radius: 5px;
				background-color: rgba(0, 0, 0, .1);
				transition: .2s;
				&:hover {
					filter: drop-shadow(1px 0px 0px #000);
				}
			}
			.record,
			.version {
				position: absolute;
				bottom: 2%;
				font-size: 150%;

				&.version {
					left: 2%;
				}

				&.record {
					right: 2%;
				}
			}
		}
		.game {
			min-width: @min;
			user-select: none;
			.btn {
				@size: 3%;
				display: inline-block;
				width: @size;
				padding-top: @size;
				overflow: hidden;
				margin: 1em;
				cursor: pointer;
				transition: .2s;
				border-radius: 50%;
				position: relative;
				z-index: 2;
				&:hover {
					filter: invert(30%)
				}
				&.sound {
					position: fixed;
					left: 0;
					background: url(../assets/icon/声音开.svg) no-repeat center;
					background-size: contain;

					&.clicked {
						background: url(../assets/icon/声音关.svg) no-repeat center;
						background-size: contain;
					}
				}
				&.setting {
					position: fixed;
					right: 0;
					background: url(../assets/icon/设置.svg) no-repeat center;
					background-size: contain;
					&:hover {
						animation: fade reverse 1s both, rotate 2s infinite linear;
					}
				}
			}
			.btn,.gameInfo{animation: fade reverse .5s;}
			.gameInfo {
				position: relative;
				z-index: 1;
				.score,
				.aliveTime {
					font-size: 2vw;
					position: fixed;
					top: 2.5%;
					width: 100%;
					text-align: center;
					&.aliveTime {
						text-align: left;
						padding-left: 10%;
					}
				}
				.version {
					color: #d4d4d4;
					position:fixed;
					bottom: 2%;left: 2%;
				}
			}
		}
		.allPanel{
			min-width: 740px;
			position: fixed;
			font-size: 2rem;
			position: absolute;
			@width:50%;width:@width;left: calc(100% - @width * 1.5);
			top: 22%;
			border-radius: 15px;
			background: rgba(199, 199, 199,.9);
			backdrop-filter: blur(2px);
			padding: 1em 2em;
			line-height: 1.5em;
			box-sizing: border-box;
			height: 50vh;
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
			.close{
				position: fixed;bottom: 5%;left: 40%;
				--c1:red;--c2:white;
				display: block;
				background: var(--c1);
				padding: .25em 1em;
				border-radius: 5px;
				color: var(--c2);
			}
		}
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
				&:hover{box-shadow: 1px -1px 0px #000,-1px 1px 0px #000;}
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
					@bx:2px;
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
		.failedPanel{
			.close{bottom:80%;left:80%;}
			.again{
				--c1:limegreen;
				--c2:white;
				position: absolute;
				bottom: 5%;right: 5%;
				padding: .25em 2.4em;
				background: var(--c1);
				color: var(--c2);
			}
			.back{
				position: absolute;
				bottom: 5%;left: 5%;
				padding: .25em 5em;
			}
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
	@keyframes rotate {
		0% {
			transform: rotate(0deg)
		}

		100% {
			transform: rotate(360deg)
		}
	}
</style>