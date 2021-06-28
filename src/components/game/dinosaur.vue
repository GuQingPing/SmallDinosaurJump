<template>
	<div class="contentBox">
		<div id="dinosaur" :style="style" :class="cName">
			<div class="hints">
				<span v-for="x in hints" :style="{'color':x.color}" :key="x.index">
					{{x.text}}
				</span>
			</div>
			<div class="head"><!--头部绘制-->
				<div class="eye"></div>
				<div class="part-1"></div>
				<div class="part-2"></div>
				<div class="part-3"></div>
				<div class="part-4"></div>
			</div>
			<div class="body"><!--身体绘制-->
				<div class="part-1"></div>
				<div class="part-2"></div>
				<div class="part-3"></div>
				<div class="part-4"></div>
			</div>
			<div class="tail"><!--尾巴绘制-->
					<div class="part-1"></div>
					<div class="part-2"></div>
					<div class="part-3"></div>
					<div class="part-4"></div>
					<div class="part-5"></div>
			</div>
			<div class="hand"><!--手部绘制-->
				<div class="part-1"></div>
				<div class="part-2"></div>
			</div>
			<div class="leg"><!--腿部绘制-->
				<div class="left">
					<div class="part-1"></div>
					<div class="part-2"></div>
					<div class="part-3"></div>
				</div>
				<div class="right">
					<div class="part-1"></div>
					<div class="part-2"></div>
				</div>
			</div>
		</div>
		<div class="particle"><!--粒子效果-->
			<span v-for="x in particle" :style="width=x.style"></span>
		</div>
	</div>
</template>

<script>
	export default {
		name: 'dinosaur',
		props:{
			showMode:{default:false}
		},
		created() {
			if(this.showMode) return;
			document.addEventListener('keydown', this.keyDown)
			document.addEventListener('keyup', this.keyUp)
		},
		mounted() {
			if(this.showMode){
				this.setDinosaurStyle("eye","white");
				return;
			}
			let game=this.$parent;
			game.start();
		},
		data() {
			return {
				cName:"",			//类(切换行走与跳跃样式)
				style:"",			//调整显示坐标
				size: 5, 			//宽高
				x: 10,y: 0,			//坐标(百分比)
				jumpSpeed:1,		//跳跃速度
				jumping: false,		//跳跃状态
				jumpHeight:25,		//跳跃最高-高度(百分比)
				canJump: true,		//可否跳跃(接触过地面则可再次跳跃)
				canFall: true,		//可否重坠
				fallCD: 0.3,		//重坠CD(秒)
				continuouJump:false,//连跳状态(长按W或↑)
				falling:false,		//重坠状态
				move_left: false,	//是否左移
				move_right: false,	//是否右移
				movespeed:.4,		//小恐龙移速
				particle:[],		//用于保存生成的粒子列表
				hints:[],			//保存提示列表
				hintIndex:1,		//提示索引
			}
		},
		methods: {
			keyDown(e) { //监听键盘按下
				if (e.code == "KeyW" | e.code == "ArrowUp" | e.code == "Space" &this.canJump){//跳跃
					this.jumping=true;			//跳跃中
					this.canJump=false;			//不可跳跃
					this.continuouJump=true;	//连跳中
					this.$parent.play("jump");
				}
				if (e.code == "KeyA" | e.code == "ArrowLeft") this.move_left = true;	//左移
				if (e.code == "KeyD" | e.code == "ArrowRight") this.move_right = true;	//右移
				if(!this.canFall||this.y<=1) return
				if (e.code == "KeyS" | e.code == "ArrowDown"){
					this.canFall=false;	//重坠进入CD
					this.jumping=false;	//取消跳跃
					this.falling=true;	//重坠
					setTimeout(()=>{
						this.canFall=true;
					},this.fallCD*1000)
	}
			},
			keyUp(e) {//监听键盘松开
				if (e.code == "KeyW" | e.code == "ArrowUp" | e.code == "Space") this.continuouJump=false;//停止连跳
				if (e.code == "KeyA" | e.code == "ArrowLeft") this.move_left = false;
				if (e.code == "KeyD" | e.code == "ArrowRight") this.move_right = false;
			},
			panel(){return this.$parent.$parent},//返回panel组件实例
			//检查
			check() {
				//清除粒子
				let l=this.particle.length;
				if(l>40) this.particle.length=0;
				//移动与销毁粒子
				for (let i = 0; i < this.particle.length; i++) {
					let item=this.particle[i];
					let game=this.$parent;
					let t=item.residenceTime;
					if(t>=0){
						item.residenceTime-=0.01;
						continue;
					}
					item.x-=game.moveSpeed;
					item.style=`width:${item.size}px;height:${item.size}px;left:${item.x}%;bottom:${item.y}%;opacity:${item.opacity}%;`;
					if(item.x<=0) this.particle.splice(i,1);
				}
				//操作
				if(this.$parent.visible) return;//若游戏不在运行中，禁止操作
				let movespeed=this.movespeed,game=this.$parent;
				if (this.move_left) this.move(-movespeed);
				if (this.move_right) this.move(movespeed);
				if (this.jumping) this.jump();
				if (this.continuouJump&this.canJump) this.keyPress("KeyW");//连跳 
				if (!this.jumping&&!this.canJump) this.fall();
				this.style=`left:${this.x}%;bottom:${40.6+this.y}%`;
			},
			//跳跃
			jump() {
				if (this.y>this.jumpHeight) {
					this.jumping=false;
					return;
				}
				this.y += this.jumpSpeedAmend(this.jumpSpeed);
				this.cName="jumping";
			},
			//下坠
			fall() {
				if (this.y-this.jumpSpeedAmend(this.jumpSpeed)<0 && !this.canJump) {
					this.y=0;
					this.cName="";
					this.canJump=true;
					this.$parent.play("falled",4);
					if(this.falling){
						this.$parent.play("falled");
						this.createParticle();//重坠粒子效果
					}
					this.falling=false;
					return;
				}
				this.y-= this.jumpSpeedAmend(this.jumpSpeed);
				if(this.falling) this.y-=this.jumpSpeed*1.2;
			},
			//升坠速度修正
			jumpSpeedAmend(speed){
				let difficulty=this.panel().difficulty;
				//加快起跳速度
				if(this.y<this.jumpHeight*0.4) speed*=1.5;
				//模拟惯性(越接近跳跃最高值，增减越慢)
				if(Math.abs(this.y-this.jumpHeight)<=this.jumpHeight*0.4) speed*=0.9;
				if(Math.abs(this.y-this.jumpHeight)<=this.jumpHeight*0.3) speed*=0.85;
				if(Math.abs(this.y-this.jumpHeight)<=this.jumpHeight*0.2) speed*=0.8;
				if(Math.abs(this.y-this.jumpHeight)<=this.jumpHeight*0.1) speed*=0.75;
				return speed;//返回最终速度
			},
			//移动
			move(speed) {
				if (speed > 0 &&this.x > 94) return;
				if (speed < 0 &&this.x < 1) return;
				this.x += speed;
			},
			//手动触发键盘按下事件
			keyPress(code){
				let evt = document.createEvent("UIEvents");//创建键盘事件对象
				evt.code = code;//设置键值
				evt.initEvent("keydown", true, true);//调用对象事件
				document.body.dispatchEvent(evt);//分配对象
			},
			//生成粒子
			createParticle(){
				let particleCount=this.createRandom(10,20);//生成粒子数量
				this.pX=this.x;
				for (var i = 0; i < particleCount; i++) {//循环生成粒子对象
					let o={
						x:this.createRandom(this.x-3,this.x+3),
						y:this.createRandom(0,40)-this.createRandom(0,50),
						size:this.createRandom(5,20),
						opacity:this.createRandom(10,100),
						residenceTime:0.1,
						style:"",
					};
					o.style=`width:${o.size}px;height:${o.size}px;left:${o.x}%;bottom:${o.y}%;opacity:${o.opacity}`;
					this.particle.push(o);//将对象加入数组
				}
				console.log("Now particles:"+this.particle.length);
			},
			//生成随机数-整数
			createRandom(start, end) {
				let abs = Math.abs(start - end);
				return Math.round(start + Math.round(Math.random() * abs));
			},
			//获得金币提示
			getGold(){
				this.showTips("+1","gold");
			},
			//受伤提示
			getHurt(){
				this.showTips("-1","red");
			},
			//新建提示
			showTips(str,color){
				let obj={
					text:str,
					color:color,
					index:this.hintIndex++,
				};
				this.hints.push(obj);
				if(this.hints.length>=10){
					if(this.hintIndex>=100) this.hintIndex=1;
					console.log("清除提示")
					this.hints.splice(0,this.hints.length-2);
				}
			},
			//设置恐龙样式
			/**
			 * 	@param {string} attrNmae 	要修改的变量名
			 * 	@param {type} attrValue 	要设置的值
			 * 
			 * */
			setDinosaurStyle(attrNmae,attrValue){
				let map={
					"eye":"--dinosaurEye",
					"body":"--dinosaurColor"
				};
				let root=document.documentElement;
				root.style.setProperty(map[attrNmae],attrValue);
			},
		},
		destroyed() {
			if(this.showMode) return;
			document.removeEventListener("keydown",this.keyDown);
			document.removeEventListener("keyup",this.keyUp);
			clearInterval(this.checker);
		},
	}
</script>

<style lang="less">
	:root{
		--dinosaurColor:#000;
		--dinosaurEye:red;
	}
</style>
<style lang="less" scoped>
	#dinosaur {
		position: absolute;
		bottom: 40%;
		left: 10%;
		width: calc(0.8 * 5vw);height: calc(0.8 * 5vw);
		z-index: 1;
		//绘制提示
		.hints{
			position: absolute;
			top: -70%;left: 40%;
			color: gold;
			font-size: 2vw;
			width: 3vw;height: 1em;
			span{
				position: absolute;
				top: 0%;left: 0%;
				animation: topIn .8s both;
			}
		}
		//绘制小恐龙
		.head{//绘制头部
			width: 50%;
			height: 41%;
			position: absolute;
			top: 0;right: 0%;
			.part-1,.part-2,.part-3,.part-4,.eye{
				position: absolute;
				top: 0;left: 0;
				background: var(--dinosaurColor);
			}
			.part-1{
				width: 80%;height: 55%;
				left: 12%;
			}
			.part-2{
				width: 100%;height: 50%;
				top: 10%;
			}
			.part-3{
				width: 45%;height: 45%;
				top: 50%;
			}
			.part-4{
				width: 75%;height: 20%;
				top: 75%;left: 10%;
				&::after{
					content: '';
					display: block;
					width: 70%;height: 70%;
					background: var(--dinosaurColor);
					position: absolute;
					bottom: -50%;left: -30%;
				}
			}
			.eye{
				background: var(--dinosaurEye);
				width: .3vw;height: .3vw;
				top: 17%;left: 20%;
				z-index: 2;
				box-shadow: 0px 0px 10px 3px var(--dinosaurEye);
			}
		}
		.body{//绘制身体
			width: 50%;
			height: 40%;
			position: absolute;
			top: 44%;left: 20%;
			.part-1,.part-2,.part-3,.part-4,.part-5{
				position: absolute;
				width: 70%;height: 85%;
				left: 50%;top: -4%;
				background: var(--dinosaurColor);
			}
			.part-1{left: 30%;top: -4%;}
			.part-2{left: 17%;top: 7%;}
			.part-3{left: 10%;top: 17%;}
			.part-4{left: 0%;top: 27%;}
		}
		.tail{//绘制尾巴
			position: absolute;
			width: 18%;height: 50%;
			left: 3%;top: 35%;
			.part-1,.part-2,.part-3,.part-4,.part-5{
				position: absolute;
				width: 25%;height: 50%;
				right: 0%;bottom: 40%;
				background: var(--dinosaurColor);
			}
			.part-1{right: 0%;bottom: 0%;}
			.part-2{right: 15%;bottom: 7%;}
			.part-3{right: 30%;bottom: 14%;}
			.part-4{right: 45%;bottom: 21%;}
			.part-5{
				right: 60%;bottom: 30%;
				height: 60%;
			}
		}
		.hand{//绘制手
			position: absolute;
			width: 18%;height: 14%;
			right: 14%;top: 47%;
			animation: handMove .6s infinite;
			.part-1,.part-2{
				position: absolute;
				width: 100%;height: 50%;
				left: 0%;top: 0%;
				background: var(--dinosaurColor);
				&.part-2{
					width: 40%;height: 90%;
					left: 65%;
				}
			}
		}
		.leg{//绘制腿部
			position: absolute;
			width: 30%;height: 12%;
			bottom: 0%;left: 25%;
			@time:.7s;
			.left{//左腿
				height: 100%;
				animation: walk @time infinite;
				.part-1,.part-2,.part-3{
					position: absolute;
					left: 20%;top: -100%;
					width: 20%;height: 200%;
					background: var(--dinosaurColor);
				}
				.part-2{width: 35%;height: 50%;top: 69%;}
				.part-3{width: 32%;height: 50%;}
			}
			.right{//右腿
				height: 100%;
				position: relative;
				bottom: 100%;right: -10%;
				animation: walk @time .2s infinite reverse;
				.part-1,.part-2{
					position: absolute;
					left: 53%;top: -100%;
					width: 20%;height: 200%;
					background: var(--dinosaurColor);
				}
				.part-2{width: 38%;height: 50%;top: 69%;}
			}
		}
		&.jumping .leg{
			.left,.right{
				left: 50%;
				animation: jump 1.5s;
				transform-origin: top;
			}
		}
		&.dead{animation: fade 1s,dead 2s both;}
	}
	.particle{
		position: fixed;
		bottom: 40%;left: 0%;
		width: 100%;height: 15vh;
		span{
			display: block;
			background: #000;
			border-radius: 50%;
			position: absolute;
			left: 50%;bottom: 0;
			animation: float .6s forwards linear;
		}
	}
	@keyframes float{//粒子漂浮效果
		to{bottom: 100%;opacity: 0;}
	}
	@keyframes jump{//跳跃动画
		0%{transform:translate(-20%,0%) scale(1,3.5)}
		25%{transform:translate(-20%,0%) scale(1.2,.8)}
		75%{transform:translate(-20%,0%) scale(1,.8)}
		100%{transform:translate(-20%,0%) scale(1,1)}
	}
	@keyframes walk{//行走动画
		0%{transform: rotate(-25deg) translate(55%,20%)}
		50%{transform: rotate(50deg) translate(-90%,100%)}
		100%{transform: rotate(-25deg) translate(55%,20%)}
	}
	@keyframes handMove{//手部浮动
		0%{transform:translate(0%,-10%)}
		50%{transform:translate(0%,10%)}
		100%{transform:translate(0%,-10%)}
	}
	@keyframes dead{//死亡动画
		0%{
			transform-origin: bottom;
			transform: scale(1,1);
		}
		90%{transform: scale(1,0);}
		100%{
			transform-origin: bottom;
			transform: scale(1,0);
			bottom: 40.5%;
		}
	}
</style>
