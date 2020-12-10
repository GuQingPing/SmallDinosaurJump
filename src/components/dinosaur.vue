<template>
	<div id="dinosaur" :style="style" :class="cName"></div>
</template>

<script>
	export default {
		name: 'dinosaur',
		created() {
			document.addEventListener('keydown', this.keyDown)
			document.addEventListener('keyup', this.keyUp)
			this.checker = setInterval(this.check, 10);
		},
		data() {
			return {
				cName:"",//类(切换行走与跳跃样式)
				style:"",//调整显示坐标
				size: 5, //宽高
				x: 0,y: 0, //坐标
				lagTime: 0.1, //跳跃滞空时间
				jumping: false, //跳跃状态
				canJump: false, //可否跳跃(是否接触过地面)
				move_left: false,
				move_right: false,
				checker: null,
				failed:false,
				ms:0,
				s:0,
			}
		},
		computed:{
			jumpHeight:{
				get(){
					return 20 * window.innerHeight * 0.01;
				}
			}
		},
		methods: {
			keyDown(e) { //监听键盘
				if (e.code == "KeyW" | e.code == "ArrowUp" | e.code == "Space" &&this.canJump){
					this.jumping=true;
					this.canJump=false;
					if(this.$parent.muted) return;
					let jump = document.getElementById("jump");
					jump.play();
				}
				if (e.code == "KeyA" | e.code == "ArrowLeft") this.move_left = true;
				if (e.code == "KeyD" | e.code == "ArrowRight") this.move_right = true;
			},
			keyUp(e) {
				if (e.code == "KeyW" | e.code == "ArrowUp" | e.code == "Space") this.jumping=false;
				if (e.code == "KeyA" | e.code == "ArrowLeft") this.move_left = false;
				if (e.code == "KeyD" | e.code == "ArrowRight") this.move_right = false;
			},
			//检查
			check() {
				//操作
				if (this.move_left) this.move(-1);
				if (this.move_right) this.move(1);
				if (this.jumping) this.jump();
				if (!this.jumping&&!this.canJump) this.fall();
				this.setStyle();
				//计秒
				this.ms+=10;
				if(this.ms>=1000){
					this.ms=0;
					if(!this.failed) this.flushTime();
				}
				if(this.$parent.model!="run"||this.failed) return;
				//检查障碍物
				let cactus=document.getElementsByClassName("cactus")[0].getElementsByTagName("span");
				for (var i = 0; i < cactus.length; i++) {
					let w=window.innerHeight;
					let ele=cactus[i];
					let amend=0.45;//修正系数
					if(ele.classList.contains("c4")) amend=0.5;
					let eLeft=ele.offsetLeft+(ele.clientWidth*amend);
					let eRight=eLeft+ele.clientWidth-(ele.clientWidth*amend*2);
					let eTop=ele.clientHeight*0.7;
					let d=document.getElementById("dinosaur");
					let dWidth=(w*this.size*0.01);
					let dLeft=d.offsetLeft+(dWidth/5);
					let dRight=dLeft+dWidth-(dWidth/5*2);
					//销毁
					
					//碰撞
					if(dRight>=eLeft&&this.y<=eTop&&dLeft<=eRight){
						let score=document.getElementsByClassName("score")[0].innerHTML;
						let heighest=localStorage.getItem("score");
						if(score>heighest) localStorage.setItem("score",score);
						this.failed=true;
						this.$parent.failedPanel=true;
						this.$parent.record=this.$parent.score;
						localStorage.setItem("record",this.$parent.score);
					}
				}
			},
			//计时与得分
			flushTime(){
				let seconds=++this.s;
				let mintues=parseInt(seconds/60);
				let hours=parseInt(mintues/60);
				let days=parseInt(hours/24);
				if(mintues>=1) seconds-=mintues*60;
				if(hours>=1) mintues-=hours*60;
				if(days>=1) hours-=days*24;
				this.$parent.aliveTime=days+":"+hours+":"+mintues+":"+seconds;
				let timeScoreAmend=1+parseInt(this.s/10);
				let difficultyScoreAmend=1;
				let difficulty=this.$parent.difficulty;
				if(difficulty=="easy") difficultyScoreAmend=0.5;
				if(difficulty=="hard") difficultyScoreAmend=2;
				if(difficulty=="hell") difficultyScoreAmend=4;
				this.$parent.score=this.s*10*timeScoreAmend*difficultyScoreAmend;
			},
			//跳跃
			jump() {
				if (this.y > this.jumpHeight) {
					this.jumping=false;
					return;
				}
				let difficulty=this.$parent.difficulty;
				let jumpSpeed=5;
				if(difficulty=="easy") jumpSpeed=4;
				if(difficulty=="hard") jumpSpeed=6;
				if(difficulty=="hell") jumpSpeed=7;
				this.y += jumpSpeed;
				this.cName="jumping";
			},
			//下坠
			fall() {
				if (this.y<0) {
					this.cName="";
					this.canJump=true;
					return;
				}
				let difficulty=this.$parent.difficulty;
				let fallSpeed=6;
				if(difficulty=="easy") fallSpeed=5;
				if(difficulty=="hard") fallSpeed=7;
				if(difficulty=="hell") fallSpeed=8;
				this.y -= fallSpeed;
			},
			//移动
			move(num) {
				let w = window.innerWidth;
				let speed = 2 * 0.001 * w;
				let nowX = w*0.1+this.x;
				if (num > 0 &&nowX < (w-w*0.06)) this.x += speed;
				if (num < 0 &&nowX > 10) this.x -= speed;
			},
			//设置显示坐标
			setStyle(){
				let w = window.innerWidth;
				let h = window.innerHeight;
				let ground = h * .4;
				this.style=`left:${w*0.1+this.x}px;bottom:${ground+this.y}px`;
			},
			panel(){//返回面板属性
				return this.$parent.$data;
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
		background-image: url(../assets/img/dinosaur.png);
		background-repeat: no-repeat;
		background-size: contain;
		filter: invert(100%);
		animation:fade reverse 1s both,walk .8s infinite;
		&.jumping {
			animation:fade 1s;
		}
		&::before {
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
	@keyframes walk {
		@n: 1.02; //缩放比例
		0% {background-image: url(../assets/img/d1.png)}
		25% {background-image: url(../assets/img/d1.png)}
		25.1% {background-image: url(../assets/img/d2.png);transform: scale(1, @n)}
		50% {background-image: url(../assets/img/d2.png);transform: scale(1, @n)}
		50.1% {background-image: url(../assets/img/d3.png);transform: scale(1, @n)}
		75% {background-image: url(../assets/img/d3.png);transform: scale(1, @n)}
		75.1% {background-image: url(../assets/img/d4.png)}
		100% {background-image: url(../assets/img/d4.png)}
	}
</style>
