<template>
	<div id="dinosaur">
	</div>
</template>

<script>
	export default {
		name: 'dinosaur',
		created() {
			document.addEventListener('keydown', this.keyDown)
			document.addEventListener('keyup', this.keyUp)
			window.addEventListener("resize", this.resize);
			this.checker = setInterval(this.check, 10);
		},
		data() {
			return {
				width: 5, //宽度
				height: this.width,
				x: 0,y: 0, //坐标
				lagTime: 0.2, //跳跃滞空时间
				jumping: false, //跳跃中
				w: window.innerWidth,
				h: window.innerHeight,
				move_left: false,
				move_right: false,
				checker: null,
				ms:0,
				s:0,
			}
		},
		computed:{
			jumpHeight:{
				get(){
					return 20 * this.h * 0.01;
				}
			}
		},
		methods: {
			keyDown(e) { //监听键盘
				if (e.code == "KeyW" | e.code == "ArrowUp" | e.code == "Space" && !this.jumping) this.jump();
				if (e.code == "KeyS" | e.code == "ArrowDown") this.fall();
				if (e.code == "KeyA" | e.code == "ArrowLeft") this.move_left = true;
				if (e.code == "KeyD" | e.code == "ArrowRight") this.move_right = true;
			},
			keyUp(e) {
				if (e.code == "KeyA" | e.code == "ArrowLeft") this.move_left = false;
				if (e.code == "KeyD" | e.code == "ArrowRight") this.move_right = false;
			},
			//检查
			check() {
				let cactus=document.getElementsByClassName("cactus")[0].getElementsByTagName("span");
				for (var i = 0; i < cactus.length; i++) {
					let ele=cactus[i];
					let amend=0.45;//修正系数
					if(ele.classList.contains("c4")) amend=0.5;
					let eLeft=ele.offsetLeft+(ele.clientWidth*amend);
					let eRight=eLeft+ele.clientWidth-(ele.clientWidth*amend*2);
					let eTop=ele.clientHeight*0.7;
					let d=document.getElementById("dinosaur");
					let dWidth=(this.w*this.width*0.01);
					let dLeft=d.offsetLeft+(dWidth/5);
					let dRight=dLeft+dWidth-(dWidth/5*2);
					if(dRight>=eLeft&&this.y<=eTop&&dLeft<=eRight){
						let score=document.getElementsByClassName("score")[0].innerHTML;
						let heighest=localStorage.getItem("score");
						if(score>heighest) localStorage.setItem("score",score)
						alert("被扎到了，完蛋...");
						location.reload();
					}
				}
				if (this.move_left) this.move(-1);
				if (this.move_right) this.move(1);
				this.ms+=10;
				if(this.ms>=1000){
					this.ms=0;
					this.flushTime();
				}
				let muted = document.getElementsByClassName("sound")[0].classList.contains("clicked");
				let arr=document.getElementsByClassName("sound")[0].getElementsByTagName("audio");
				for (var i = 0; i < arr.length; i++) {
					if(muted) arr[i].volume=0;
					else arr[i].volume=1;
				}
			},
			//计时
			flushTime(){
				let aliveTime=document.getElementsByClassName("aliveTime")[0];
				let score=document.getElementsByClassName("score")[0];
				let seconds=this.s++;
				let mintues=parseInt(seconds/60);
				let hours=parseInt(mintues/60);
				let days=parseInt(hours/24);
				if(mintues>=1) seconds-=mintues*60;
				if(hours>=1) mintues-=hours*60;
				if(days>=1) hours-=days*24;
				aliveTime.innerHTML=days+":"+hours+":"+mintues+":"+seconds;
				score.innerHTML=this.s*15*1;
			},
			//跳跃
			jump() {
				let h = this.h;
				let jumpHeight = this.jumpHeight; //跳跃高度
				let ele = document.getElementById("dinosaur");
				this.jumping = true;
				ele.classList.add("jumping");
				let jump = document.getElementById("jump");
				jump.currentTime = 0;
				jump.play();
				let ground = h * .4;
				let up = setInterval(() => {
					ele.style.bottom = ground + this.y + "px";
					ele.setAttribute("y", this.y);
					this.y += 2;
					if (this.y > jumpHeight) {
						clearInterval(up);
						setTimeout(this.fall, 100);
					}
				}, 5);
			},
			//降落
			fall() {
				let h = this.h;
				let ground = h * .4;
				let ele = document.getElementById("dinosaur");
				let down = setInterval(() => {
					if (this.y > 0) {
						this.y -= 2;
						if (this.jumping)
							this.y -= 2;
						ele.style.bottom = ground + this.y + "px";
						ele.setAttribute("y", this.y);
						return;
					}
					clearInterval(down);
					this.jumping = false;
					ele.classList.remove("jumping");
				}, 15);
			},
			//移动
			move(num) {
				let ele = document.getElementById("dinosaur");
				let w = this.w;
				let speed = 2 * 0.001 * w;
				let nowX = ele.style.left.replace("px", '');
				if (nowX < 1 && num < 0) return;
				if (nowX > (w - w * 0.06) && num > 0) return;
				if (num > 0) ele.style.left = (w * 0.1) + (this.x += speed) + "px";
				if (num < 0) ele.style.left = (w * 0.1) + (this.x -= speed) + "px";
			},
			//重载
			resize() {
				let w = this.w;
				let h = this.h;
				let width = this.width;
				let ele = document.getElementById("dinosaur");
				ele.style.width = w * width * 0.01 + "px";
				ele.style.height = w * width * 0.01 + "px";
			}
		},
		destroyed() {
			document.removeEventListener("keydown");
			document.removeEventListener("keyup");
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
		animation: scaleWidth 1s .2s both, walk .8s infinite;
		&.jumping {
			animation: scaleWidth 1s .2s both;
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

	@keyframes scaleWidth {
		0% {transform: scale(0, 1);}
		100% {transform: scale(1, 1);}
	}
</style>
