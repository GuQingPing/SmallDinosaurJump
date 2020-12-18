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
		<infoPanel :infoPanel="infoPanel" :infoPanelText="infoPanelText"></infoPanel>
		<settingPanel :visible="visible" :settingPanel="settingPanel" :dcList="dcList"></settingPanel>
		<failedPanel :visible="visible" :failedPanel="failedPanel" :aliveTime="aliveTime" :score="score" :difficulty="difficulty"></failedPanel>
	</div>
</template>

<script>
	import game from './game.vue'
	import allPanel from './panel/allPanel.vue'
	import infoPanel from './panel/infoPanel.vue'
	import failedPanel from './panel/failedPanel.vue'
	import settingPanel from './panel/settingPanel.vue'
	export default {
		components: {failedPanel,settingPanel,infoPanel,game},
		data() {
			return {
				version: "betaV0.4.6",	//版本信息
				visible: true,			//显示开始面板/显示游戏
				aliveTime: "0:0:0:0",	//存活时间
				totalSeconds:0,
				score: 0,				//分数
				record:localStorage.getItem("record") ?? 0,//本地最高记录
				inputVolume:80,			//音量
				difficulty:"easy",		//难度
				model:"run",			//运行模式
				settingPanel:false,		//是否显示设置面板
				failedPanel:false,		//是否显示失败面板
				infoPanel:false,		//是否显示提示面板
				infoPanelText:"未开发",	//提示面板默认信息
				dcList:[				//难度按钮列表
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
				this.setVolume();
			},
			difficulty(nV,oV){
				localStorage.setItem("difficulty",nV);
			},
			model(nV,oV){
				localStorage.setItem("model",nV);
			},
		},
		computed: {
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
				if(c.contains("btn")){//音效
					let click=document.getElementById("click");
					click.volume=this.volume/4;
					click.currentTime=0;
					click.play();
				}
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
				}
				if (c.contains("about")) {//关于
					this.infoPanelText=`
					<div style="line-height:1.5em;">
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
				if (e.code == "Space" && this.visible) this.visible = false;//开始游戏
				if (e.code == "Enter" && !this.visible && this.failedPanel) this.playAgain();//重来
				if (e.code == "Slash"){
					this.model = this.model=="run"?"debug":"run";	
				}
			},
			hover(e){//按钮悬停音效
				let c = e.target.classList;
				if(c.contains("btn")){
					document.getElementById("hover").currentTime=0;
					document.getElementById("hover").play();
				}
			},
			setVolume(){//设置音量
				let sound=document.getElementsByClassName("sound")[0]
				let clicked=sound.classList.contains("clicked");
				let audio=sound.getElementsByTagName("audio");
				if(this.volume==0&&!clicked) sound.classList.add("clicked");
				if(this.volume>0&&clicked) sound.classList.remove("clicked");
				for (var i = 0; i < audio.length; i++) {
					audio[i].volume=this.volume;
				}
				document.getElementById("falled").volume=this.volume/3;
			},
			playAgain(){//重玩
				this.failedPanel=false;
				this.visible=true;
				setTimeout(()=>{
					this.visible=false;
				},100)
			},
			getSetting(){//获取设置
				this.inputVolume=localStorage.getItem("volume")??this.inputVolume;
				this.difficulty=localStorage.getItem("difficulty")??this.difficulty;
				this.model=localStorage.getItem("model")??this.model;
			},
			untapped(){//未开发
				this.infoPanelText=`未开发`;
				this.infoPanel=true;
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