<template>
	<div id="panel" @click="click" @mouseover="hover">
		<div v-if='model!="run"' class="model">{{model}}</div>
		<div v-if="visible" class="ui">
			<div class="title"><span></span>小恐龙跳跃</div>
			<div class="btn start">开始游戏</div>
			<div class="btn skin">皮肤/地图</div>
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
		<skinPanel></skinPanel>
		<shopPanel></shopPanel>
		<achievementPanel></achievementPanel>
		<settingPanel></settingPanel>
		<failedPanel></failedPanel>
		<infoPanel></infoPanel>
	</div>
</template>

<script>
	import game from './game/game.vue'
	import infoPanel from './panel/infoPanel.vue'
	import failedPanel from './panel/failedPanel.vue'
	import settingPanel from './panel/settingPanel.vue'
	import shopPanel from './panel/shopPanel.vue'
	import skinPanel from './panel/skinPanel.vue'
	import achievementPanel from './panel/achievementPanel.vue'
	export default {
		components: {failedPanel,settingPanel,game,infoPanel,shopPanel,achievementPanel,skinPanel},
		data() {
			return {
				version: "betaV0.5.3",											//版本信息
				date: "2021/5/23",												//编写时间
				visible: true,													//显示开始面板/显示游戏
				aliveTime: "0:0:0:0",											//存活时间
				totalSeconds:0,													//总秒数
				score: 0,														//分数
				record:localStorage.getItem("record") ?? 0,						//本地最高记录
				inputVolume:100,												//主音量
				inputMusic:60,													//音乐
				inputSound:80,													//音效
				difficulty:"easy",												//难度
				model:"run",													//运行模式
				settingPanel:false,												//设置面板
				failedPanel:false,												//失败面板
				infoPanel:false,												//信息面板
				shopPanel:false,												//商店面板
				skinPanel:false,												//皮肤面板
				achievementPanel:false,											//成就面板
				goldCoins:parseInt(localStorage.getItem("gold-coins") ?? 0),	//金币数量
				closeState:true,												//判断可否关闭面板
				soundSwitch:{													//音效的单独开关
					click:true,													//点击音效
					hover:true,													//经过音效
				}
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
				if(c.contains("btn")&&this.soundSwitch.click){//点击音效
					this.play("click",0.25);
				}
				if (c.contains("start")) {//开始游戏
					this.visible = false;
				}
				if (c.contains("sound")) {//声音
				let sound=document.getElementsByClassName("sound")[0];
				let clicked=sound.classList.contains("clicked");
					this.inputVolume = clicked?100:0;
					clicked?sound.classList.remove("clicked"):sound.classList.add("clicked");
				}
				if (c.contains("setting")) this.set("settingPanel",true);//设置
				if (c.contains("about")) this.set("infoPanel",true);//关于面板
				if (c.contains("skin")) this.set("skinPanel",true);//面板
				if (c.contains("shop")) this.set("shopPanel",true);//商城面板
				if (c.contains("achievement")) this.set("achievementPanel",true);//成就面板
			},
			keyDown(e) {//键盘按下
				console.log(e.code+" "+e.keyCode+" is paused");
				if(e.code == "Escape") this.autoBack();//ESC返回/关闭
				if (e.code == "Space" && this.visible) this.visible = false;//开始游戏
				if (e.code == "Enter" && !this.visible && this.failedPanel) this.playAgain();//重来
				if (e.code == "Slash"){
					this.model = this.model=="run"?"debug":"run";	
				}
			},
			hover(e){//按钮悬停音效
				let c = e.target.classList;
				if(c.contains("btn")&&this.soundSwitch.hover){
					this.play("hover",1);
				}
			},
			autoBack(){//ESC键 自动返回/关闭
				for (let x in this.$data) {
					if(x.indexOf("Panel")!=-1){
						let v=this.$data.[`${x}`];
						if(x=="shopPanel"){//商店面板中优先关闭商品面板
							let gp=this.get("shopPanel").$children[0].$children[0];
							if(gp.panelShow){
								gp.panelShow=false;
								return;
							}
						}
						if(v) this.set(x,false);
					}
				}
				if(!this.visible&&this.closeState){
					this.set("settingPanel",true);
					this.closeState=false;
					setTimeout(()=>{this.closeState=true;},700);
				}
			},
			setStorage(){//保存 设置数据
				let o={
					mainVolume:this.inputVolume,
					musicVolume:this.inputMusic,
					soundVolume:this.inputSound,
					difficulty:this.difficulty,
					model:this.model,
					soundSwitch:this.soundSwitch,
				};
				localStorage.setItem("setting",JSON.stringify(o));
				console.log("- 配置已缓存");
				this.setVolume();
			},
			setVolume(){//设置音量
				let sound=document.getElementsByClassName("sound")[0];
				let audio=sound.getElementsByTagName("audio");
				this.inputVolume==0?sound.classList.add("clicked"):sound.classList.remove("clicked");
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
				},100);
			},
			getSetting(){//获取设置
				let setting=JSON.parse(localStorage.getItem("setting"))??null;//读取缓存配置
				if(!setting) return;//读不到或不存在直接返回
				this.inputVolume=setting.mainVolume;
				this.inputMusic=setting.musicVolume;
				this.inputSound=setting.soundVolume;
				this.difficulty=setting.difficulty;
				this.model=setting.model;
				this.soundSwitch=setting.soundSwitch;
			},
			play(name,volume){//播放音频
				let audio=document.getElementById(name);
				audio.currentTime=0;
				audio.play();
			},
			set(param,v){//设置
				if(this[`${param}`]==undefined) return;
				this[`${param}`] = v;
				if(param=="settingPanel"&&!this.visible){
					this.get("game").toggleState();
				}
			},
			get(name){//获取子组件
				for (let x of this.$children) {
					if(x.$options.name==name) return x;
				}
				return null;
			},
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