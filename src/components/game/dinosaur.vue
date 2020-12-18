<template>
	<div ref="dinosaur" id="dinosaur" :x="x" :y="y" :style="style" :class="cName">
		<div class="particles">
			<span v-for="x in particles" class="particle"></span>
		</div>
	</div>
</template>

<script>
	export default {
		name: 'dinosaur',
		created() {
			document.addEventListener('keydown', this.keyDown)
			document.addEventListener('keyup', this.keyUp)
			this.checker = setInterval(this.check, 10);
			this.cactus().add();
		},
		data() {
			return {
				cName:"",//类(切换行走与跳跃样式)
				style:"",//调整显示坐标
				size: 5, //宽高
				x: 10,y: 0, //坐标(百分比)
				jumping: false, //跳跃状态
				jumpHeight:25,//跳跃最高-高度(百分比)
				canJump: true, //可否跳跃(接触过地面则可再次跳跃)
				move_left: false,//是否左移
				move_right: false,//是否右移
				checker: null,//保存定时器
				failed:false,//是否失败
				particles:[],//粒子列表
				ms:0,//毫秒
				s:0,//秒
			}
		},
		computed:{//精确坐标(px)
			exactX:{get(){return this.x*window.innerWidth* 0.01;}},
			exactY:{get(){
				let ground=window.innerHeight*.4;
				return ground+this.y*window.innerHeight*.01;
			}},
		},
		methods: {
			keyDown(e) { //监听键盘按下
				if (e.code == "KeyW" | e.code == "ArrowUp" | e.code == "Space" &&this.canJump){//跳跃
					this.jumping=true;
					this.canJump=false;
					document.getElementById("jump").play();
				}
				if (e.code == "KeyA" | e.code == "ArrowLeft") this.move_left = true;//左移
				if (e.code == "KeyD" | e.code == "ArrowRight") this.move_right = true;//右移
			},
			keyUp(e) {//监听键盘松开
				if (e.code == "KeyW" | e.code == "ArrowUp" | e.code == "Space") this.jumping=false;
				if (e.code == "KeyA" | e.code == "ArrowLeft") this.move_left = false;
				if (e.code == "KeyD" | e.code == "ArrowRight") this.move_right = false;
			},
			panel(){return this.$parent.$parent},//返回panel组件实例
			cactus(){return this.$parent.$children[1]},//放回cactus组件实例
			//检查
			check() {
				//操作
				if (this.move_left) this.move(-1);
				if (this.move_right) this.move(1);
				if (this.jumping) this.jump();
				if (!this.jumping&&!this.canJump) this.fall();
				this.style=`left:${this.exactX}px;bottom:${this.exactY}px`;
				this.cactus().check();//移动与刷新仙人掌
				//获取仙人掌组
				let cactus=this.cactus().$data.cactus;
				for (var i = 0; i < cactus.length; i++) {
					let c=cactus[i];
					let crashAmend=2;
					let dLeft=this.x;					let dRight=this.x+this.size-2;
					let dBottom=this.y+1.5;
					let cLeft=c.x+crashAmend;			let cRight=c.x+c.width-crashAmend*2;
					let cTop=c.height*0.3;
					//碰撞
					if(dRight>=cLeft&&dLeft<=cRight&&dBottom<=cTop){
						if(this.panel().model!="run"||this.failed) return;//如果失败或者debug模式就不判定碰撞了
						clearInterval(this.checker);
						let score=this.panel().score;
						let heighest=localStorage.getItem("record")??0;
						document.getElementById("failed").play();
						if(score>heighest){
							localStorage.setItem("record",score);
							this.panel().record=this.panel().score;
						}
						this.failed=true;
						this.panel().failedPanel=true;
					}
				}
				//计秒
				if(this.failed) return;//如果失败就不刷新时间了
				this.ms+=10;
				if(this.ms%200==0) this.flushScore();
				if(this.ms%1000==0) this.flushTime();
			},
			//计时
			flushTime(){
				let seconds=++this.s;
				let mintues=parseInt(seconds/60);
				let hours=parseInt(mintues/60);
				let days=parseInt(hours/24);
				if(mintues>=1) seconds-=mintues*60;
				if(hours>=1) mintues-=hours*60;
				if(days>=1) hours-=days*24;
				this.panel().aliveTime=days+":"+hours+":"+mintues+":"+seconds;
				this.panel().totalSeconds=this.s;
			},
			//得分
			flushScore(){
				let timeScoreAmend=1+(this.s/20);
				let difficultyScoreAmend=0.5;
				let difficulty=this.panel().difficulty;
				if(difficulty=="normal") difficultyScoreAmend=1;
				if(difficulty=="hard") difficultyScoreAmend=2;
				if(difficulty=="hell") difficultyScoreAmend=3;
				this.panel().score=parseInt((this.ms/100)*timeScoreAmend*difficultyScoreAmend);
			},
			//跳跃
			jump() {
				if (this.y>this.jumpHeight) {
					this.jumping=false;
					return;
				}
				this.y += this.speedAmend(.7);
				this.cName="jumping";
				this.particles.length=0;
			},
			//下坠
			fall() {
				if (this.y-this.speedAmend(.7)<0 && !this.canJump) {
					this.y=0;
					this.cName="";
					this.canJump=true;
					document.getElementById("falled").play();
					this.particles.push("1");
					return;
				}
				this.y-= this.speedAmend(.7);
			},
			//速度修正
			speedAmend(speed){
				let difficulty=this.panel().difficulty;
				//根据难度增减升坠速度
				if(difficulty=="normal") speed*=1.2;
				if(difficulty=="hard") speed*=1.3;
				if(difficulty=="hell") speed*=1.4;
				//加快起跳速度
				if(this.y<this.jumpHeight*0.4) speed*=1.2;
				//模拟惯性(越接近跳跃最高值，增减越慢)
				if(Math.abs(this.y-this.jumpHeight)<=this.jumpHeight*0.4) speed*=0.9;
				if(Math.abs(this.y-this.jumpHeight)<=this.jumpHeight*0.3) speed*=0.85;
				if(Math.abs(this.y-this.jumpHeight)<=this.jumpHeight*0.2) speed*=0.8;
				if(Math.abs(this.y-this.jumpHeight)<=this.jumpHeight*0.1) speed*=0.75;
				return speed;//返回最终速度
			},
			//移动
			move(num) {
				let speed = 0.25;
				if (num > 0 &&this.x < 95) this.x += speed;
				if (num < 0 &&this.x > 1) this.x -= speed;
			},
		},
		destroyed() {
			document.removeEventListener("keydown",this.keyDown);
			document.removeEventListener("keyup",this.keyUp);
			clearInterval(this.checker);
		}
	}
</script>

<style lang="less" scoped>
	#dinosaur {
		position: fixed;
		bottom: 40%;
		left: 10%;
		width: 5vw;
		height: 5vw;
		background-image: url(../../assets/img/dinosaur.png);
		background-repeat: no-repeat;
		background-size: contain;
		filter: invert(100%);
		animation:fade reverse 1s both,walk .8s infinite;
		z-index: 1;
		&.jumping {
			animation:fade 1s;
		}
		&::before {//眼睛
			content: '';
			width: 5%;
			padding-top: 5%;
			border-radius: 50%;
			background: red;
			position: absolute;
			top: 11%;
			right: 38%;
		}
	}
	.particles{
		position: fixed;
		bottom: 0%;left: 0%;
		width: 100%;height: 20vh;
		.particle{//落地粒子
			position: absolute;
			bottom: 0;left: 0;
			width: 100%;height: 100%;
			@particleColor:rgba(226, 226, 226, 0.3);
			@particleSize:30px;
			animation:falled 1s ease-out both;
			background-image: radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%),
							  radial-gradient(circle,@particleColor 20%, transparent 30%);
			background-size:@particleSize @particleSize;
			background-repeat: no-repeat;
		}
	}
	@keyframes falled{
		0%{
			background-position:30% 150%;
		}
		50%{
			background-position:0% 90%,
								20% 80%,
								22% 20%,
								24% 90%,
								26% 20%,
								28% 40%,
								30% 60%,
								32% 40%,
								24% 40%,
								32% 50%,
								32% 60%,
								58% 60%,
								88% 52%,
								99% 87%,
								92% 94%,
								52% 1%,
								62% 60%,
								22% 70%,
								68% 5%,
								29% 10%,
								77% 15%,
								66% 7%,
								11% 90%,
								22% 2%,
								33% 90%,
								59% 60%,
								45% 0%,
								12% 70%,
								18% 0%,
								40% 0%;
		}
		100%{
			background-size: 0 0;
			background-position:8% 90%,
								10% 80%,
								15% 0%,
								13% 90%,
								16% 20%,
								26% 0%,
								32% 60%,
								46% 40%,
								37% 40%,
								86% 0%,
								94% 0%,
								92% 60%,
								12% 0%,
								25% 80%,
								42% 90%,
								59% 0%,
								24% 60%,
								19% 70%,
								5% 0%,
								94% 0%,
								88% 0%,
								73% 0%,
								45% 0%,
								58% 90%,
								49% 0%,
								82% 90%,
								72% 60%,
								90% 0%,
								50% 70%,
								70% 0%,
								80% 0%;
		}
	}
	@keyframes walk {
		@n: 1.02; //缩放比例
		0% {background-image: url(../../assets/img/d1.png)}
		25% {background-image: url(../../assets/img/d1.png)}
		25.1% {background-image: url(../../assets/img/d2.png);transform: scale(1, @n)}
		50% {background-image: url(../../assets/img/d2.png);transform: scale(1, @n)}
		50.1% {background-image: url(../../assets/img/d3.png);transform: scale(1, @n)}
		75% {background-image: url(../../assets/img/d3.png);transform: scale(1, @n)}
		75.1% {background-image: url(../../assets/img/d4.png)}
		100% {background-image: url(../../assets/img/d4.png)}
	}
</style>
