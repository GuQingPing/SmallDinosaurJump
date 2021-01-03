<template>
	<div id="panel" @click="click" @mouseover="hover">
		<div v-if='model!="run"' class="model">{{model}}</div>
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
			<div class="record">本地记录:{{record}}</div>
		</div>
		<game :visible="visible" :aliveTime="aliveTime" :score="score" :difficulty="difficulty"></game>
		<infoPanel></infoPanel>
		<settingPanel></settingPanel>
		<failedPanel></failedPanel>
		<div ref="a">
			<div id="b"></div>
		</div>
	</div>
</template>

<script>
	import game from './game/game.vue'
	import infoPanel from './panel/infoPanel.vue'
	import failedPanel from './panel/failedPanel.vue'
	import settingPanel from './panel/settingPanel.vue'
	export default {
		components: {failedPanel,settingPanel,game,infoPanel},
		data() {
			return {
				version: "betaV0.4.9",	//版本信息
				date: "2021/1/3",		//编写时间
				visible: true,			//显示开始面板/显示游戏
				aliveTime: "0:0:0:0",	//存活时间
				totalSeconds:0,
				score: 0,				//分数
				record:localStorage.getItem("record") ?? 0,//本地最高记录
				inputVolume:80,			//主音量
				inputMusic:80,			//音乐
				inputSound:80,			//音效
				difficulty:"easy",		//难度
				model:"run",			//运行模式
				settingPanel:false,		//是否显示设置面板
				failedPanel:false,		//是否显示失败面板
				infoPanel:false,		//是否显示信息面板
			}
		},
		watch:{
			inputVolume(v){localStorage.setItem("mainVolume",v);this.setVolume();},
			inputMusic(v){localStorage.setItem("musicVolume",v);this.setVolume();},
			inputSound(v){localStorage.setItem("soundVolume",v);this.setVolume();},
			difficulty(v){localStorage.setItem("difficulty",v);},
			model(v){localStorage.setItem("model",v);},
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
				if(c.contains("btn")){//音效
					this.play("click",0.25);
				}
				if (c.contains("start")) {//开始游戏
					this.visible = false;
				}
				if (c.contains("sound")) {//声音
				let sound=document.getElementsByClassName("sound")[0];
				let clicked=sound.classList.contains("clicked");
					this.inputVolume = clicked?100:0;
				}
				if (c.contains("setting")) this.set("settingPanel",true);//设置
				if (c.contains("about")) this.set("infoPanel",true);//关于面板
				if (c.contains("shop")) this.set("shopPanel",true);//商城面板
				if (c.contains("achievement")) this.set("achievementPanel",true);//成就面板
			},
			keyDown(e) {//键盘按下
				if (e.code == "Space" && this.visible) this.visible = false;//开始游戏
				if (e.code == "Enter" && !this.visible && this.failedPanel) this.playAgain();//重来
				if (e.code == "Slash"){
					this.model = this.model=="run"?"debug":"run";	
				}
			},	
			hover(e){//按钮悬停音效
				let c = e.target.classList;
				if(c.contains("btn")){
					this.play("hover",1);
				}
			},
			setVolume(){//设置音量
				let sound=document.getElementsByClassName("sound")[0];
				let clicked=sound.classList.contains("clicked");
				let audio=sound.getElementsByTagName("audio");
				if(this.volume/100==0&&!clicked) sound.classList.add("clicked");
				if(this.volume/100>0&&clicked) sound.classList.remove("clicked");
				for (let i of audio) {
					i.volume=this.inputVolume/100;
					if(i.classList.contains("music")) i.volume*=(this.inputMusic/100);
					if(i.classList.contains("sound")) i.volume*=(this.inputSound/100);
				}
				document.getElementById("falled").volume*=0.3;
			},
			playAgain(){//重玩
				this.failedPanel=false;
				this.visible=true;
				setTimeout(()=>{
					this.visible=false;
				},100)
			},
			getSetting(){//获取设置
				this.inputVolume=localStorage.getItem("mainVolume")??this.inputVolume;
				this.inputMusic=localStorage.getItem("musicVolume")??this.inputMusic;
				this.inputSound=localStorage.getItem("soundVolume")??this.inputSound;
				this.difficulty=localStorage.getItem("difficulty")??this.difficulty;
				this.model=localStorage.getItem("model")??this.model;
			},
			play(name,volume){//播放音频
				let audio=document.getElementById(name);
				audio.currentTime=0;
				audio.play();
			},
			set(param,v){//设置
				console.log(param+"="+v);
				this[`${param}`] = v;
			}
		},
	}
</script>

<style scoped lang="less">
	@min:1366px;
	#panel {
		width: 100%;
		height: 100%;
		min-width: @min;
		.model {
			position: relative;
			color: red;
			font-size: 150%;
			position: fixed;
			bottom: 7%;
			right: 2%;
			z-index: 2;
		}
		.version,.model{text-transform:capitalize;}
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