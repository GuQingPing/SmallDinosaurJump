<template>
	<div v-show="!visible" class="game">
		<div class="btnBox">
			<div class="btn sound">
				<div>
					<audio v-if="!visible" class="music" @loadstart="setVolume" id="bgm" src="../../assets/sound/bgm.mp3" autoplay loop></audio>
					<audio id="failed" class="music" src="../../assets/sound/failed.mp3"></audio>
					<audio id="jump" class="sound" src="../../assets/sound/jump.mp3"></audio>
					<audio id="click" class="sound" src="../../assets/sound/click.wav"></audio>
					<audio id="hover" class="sound" src="../../assets/sound/hover.mp3"></audio>
					<audio id="falled" class="sound" src="../../assets/sound/falled.mp3"></audio>
					<audio id="gold" class="sound" src="../../assets/sound/bgm.mp3"></audio>
				</div>
			</div>
			<div class="btn setting"></div>
		</div>
		<div class="gameInfo">
			<div class="health">
				<span v-for="x in healthy"></span>
				<span class="none" v-for="x in (maxHealthy-healthy)"></span>
			</div>
			<div class="golds">金币:{{this.$parent.goldCoins}}</div>
			<div class="aliveTime">存活时间 {{aliveTime}}</div>
			<div class="score">分数:{{score}}</div>
			<div class="version">
				<p>难度: {{difficulty}}</p>
				<p>操作: WASD ↑↓←→ 空格</p>
				<p>重玩: Enter 回车键</p>
			</div>
		</div>
		<div v-if="!visible">
			<bg :class="bgClass"></bg>
			<cactus></cactus>
			<gold></gold>
			<dinosaur></dinosaur>
		</div>
	</div>
</template>

<script>
	import dinosaur from './dinosaur.vue'
	import bg from './bg.vue'
	import cactus from './cactus.vue'
	import gold from './gold-coin.vue'
	export default {
		components:{dinosaur,bg,cactus,gold},
		name:"game",
		data(){
			return{
				maxHealthy:5,				//小恐龙最大生命值
				healthy:5,					//小恐龙当前生命值
				moveSpeed:.2,				//障碍物移速
				speedTimeAmend:.02,			//障碍物加速系数
				difficultyScoreAmend:0.5,	//存活得分难度修正
				speedIncreaseLag:10,		//障碍物加速时间间隔
				eatGolds:0,					//吃到的金币数量(用于分数计算)
				checker: null,				//保存定时器(模拟游戏主线程)
				bgClass:"running",			//背景运行状态
				inCrash:false,				//是否在障碍物中
				failed:false,				//是否失败
				ms:0,						//毫秒
				s:0,						//秒
			}
		},
		methods:{
			setVolume(){this.$parent.setVolume()},
			start(){//创建线程，重置数据
				this.checker = setInterval(this.check, 10);
				this.reSet();
			},
			check(){
				this.get("dinosaur").check();	//小恐龙刷新与碰撞
				this.get("cactus").check();		//仙人掌刷新与碰撞
				this.get("gold").check();		//金币刷新与碰撞
				//计时与得分
				if(this.failed) return;//如果失败就不刷新时间了
				this.ms+=10;
				if(this.ms%200==0) this.flushScore();
				if(this.ms%1000==0) this.flushTime();
			},
			get(name){//获取
				for (let x of this.$children) {
					if(x.$options.name==name) return x;
				}
				return null;
			},
			//计时
			flushTime(){
				let panel=this.$parent;
				let seconds=++this.s;
				let mintues=parseInt(seconds/60);
				let hours=parseInt(mintues/60);
				let days=parseInt(hours/24);
				if(mintues>=1) seconds-=mintues*60;
				if(hours>=1) mintues-=hours*60;
				if(days>=1) hours-=days*24;
				panel.aliveTime=days+":"+hours+":"+mintues+":"+seconds;
				panel.totalSeconds=this.s;
			},
			//得分
			flushScore(){
				let panel=this.$parent;
				let difficulty=panel.difficulty;
				if(difficulty=="easy") this.difficultyScoreAmend=.5;
				if(difficulty=="normal") this.difficultyScoreAmend=1;
				if(difficulty=="hard") this.difficultyScoreAmend=2;
				if(difficulty=="hell") this.difficultyScoreAmend=3;
				let timeScoreAmend=1+(this.s/20);
				panel.score=parseInt((this.ms/100*timeScoreAmend*this.difficultyScoreAmend))+this.eatGolds*10;
			},
			/**	播放音频
			*	@param {id} name				audio元素ID
			*	@param {num} speed				播放倍速(最高16)
			*	@param {num} startTime			播放开始时间(秒)
			*	@param {num} keepTime			播放持续时间(秒)
			*	@param {boolean}transition		是否启用渐隐(接近音频末尾时音量渐低)
			*	@param {num} transitionTime		离结束有多久启用渐隐(秒)
			*/
			play(name,speed=1,startTime=0,keepTime=document.getElementById(name).duration,transition=false,transitionTime=0.2){
				let audio=document.getElementById(name);
				audio.ontimeupdate=null;
				if(!audio){
					console.log("音频["+name+"]未找到");
					return;
				}
				audio.currentTime=startTime;	//当前时间
				audio.playbackRate=speed;		//播放速度
				audio.play();					//播放
				audio.ontimeupdate=()=>{
					let t=audio.currentTime,		//当前时间
						endTime=startTime+keepTime;	//结束时间
					if(transition&&(endTime-t)<=transitionTime)	//渐隐
						audio.volume*=0.7;
					if(t>=endTime){
						audio.pause();
						this.$parent.setVolume();
					}
				}
			},
			//重置数据
			reSet(){
				this.s=0;this.ms=0;
				this.$parent.aliveTime="0:0:0:0";
				this.healthy=this.maxHealthy;
				this.bgClass="running";
				console.log("游戏数据已重置(计时与生命值)");
			},
			//暂停与继续
			toggleState(){
				if(this.failed) return;
				if(this.checker!="paused"){
					clearInterval(this.checker);
					this.checker=this.bgClass="paused";
					console.log("游戏已暂停");
				}else{
					this.checker=setInterval(this.check, 10);
					this.bgClass="running";
					console.log("游戏继续");
				}
			},
		},
		props:{
			visible:{},
			aliveTime:{},
			score:{},
			difficulty:{},
		},
		destroyed(){
			this.eatGolds=0;
		}
	}
</script>

<style lang="less" scoped>
	@min:1366px;
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
				position: fixed;left: 0;
				background: url(../../assets/icon/声音开.svg) no-repeat center;
				background-size: 100%;
				&.clicked{
					background-size: 156%;
					background-position: left;
				}
			}
			&.setting {
				position: fixed;right: 0;
				background: url(../../assets/icon/设置.svg) no-repeat center;
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
			.aliveTime,
			.golds {
				font-size: 1.4vw;
				position: fixed;
				top: 3.3%;
				width: 100%;
				text-align: center;
				&.aliveTime {
					text-align: left;
					padding-left: 17%;
				}
				&.golds{
					transform: translate(35%,0%);
				}
			}
			.version {
				text-transform: capitalize;
				color: #d4d4d4;
				position:fixed;
				bottom: 2%;left: 2%;
			}
			.health{
				position:fixed;
				top: 3.1%;left: 5%;
				font-size: 1.4vw;
				width: 6em;height: 1.4em;
				display: flex;
				justify-content: space-around;
				align-items: center;
				span{
					width: 1em;height: 1.2em;
					box-sizing: border-box;
					background: #aa0000;
					border: 0.1vw solid #f4f4f4;
					border-bottom-color: #686868;
					border-right-color: #686868;
					&.none{background: #484848;}
				}
			}
		}
	}
</style>
