<template>
  <div>
      <section class="sectionOne" id="sectionOne">
          <div class="pageOne"></div>
          <div class="bg-star"></div>
          <canvas id="game" class="canvas"></canvas>
          <div class="typeBar">
              <div class="score">X100</div>
              <div class="time">10秒</div>
          </div>
      </section>
      <div class="music" :class="{'run':musicState,'stop':!musicState}" @click="toggleVideo">
          <audio src="../../static/video/music.mp3" id="music" preload="auto"></audio>
      </div>
  </div>
</template>

<script>
import Vue from "vue";
import JC from "jcscript";
export default {
  name: "HelloWorld",
  data() {
    return {
      msg: "Welcome to Your Vue.js App",
      musicState: false, //音乐播放状态
      music: {}, //音乐元素对象
      monster:{},//怪物控制对象
      arrNum:[],//关卡数组
      time:[],//时间数组
      chapterIndex:0,//关卡
      startMinX:50,//最小X位置
      startMinY:50,//最小Y位置
      startMaxX:window.screen.availWidth + 100,//最大X位置
      startMaxY:window.screen.availHeight + 300,//最大Y位置
      roundT:10000,//转一圈所需时间
      speed:30,//速度
      monsters:[],//当前关卡的所有怪物对象
    };
  },
  mounted: function() {
    Vue.nextTick(() => {
      this.music = document.getElementById("music");
      this.initGamePage();
    });
  },
  methods: {
    toggleVideo() {
      if (!this.music) return;
      if (this.music.paused) {
        this.music.play();
        this.musicState = true;
      } else {
        this.music.pause();
        this.musicState = false;
      }
    },
    canvasClick(ev){
        let e = ev || event;
        console.log(e);
        if(this.monsters.length > 0){
            let flag = true;
            this.monsters.forEach(item => {
                if(flag){
                    if((e.x > item._x && e.x < (item._x + item._width)) && (e.y > item._y && e.y < (item._y + item._height))){
                        console.log('点钟了');
                    }
                }
            })
        }
    },
    initGamePage(){
        let gameCavans = document.getElementById('game');
        gameCavans.width = document.documentElement.clientWidth;
        gameCavans.height = document.documentElement.clientHeight;
        this.monster = {
            mon1(option){
                let a = option;
                
                let img = new Image();
                img.src = './static/images/monster1.png';
                img.onload = function (){
                    jc.start(a.canvas);
                    jc.image(img,-100,-100,109,114).id(a.id).level(2);
                    jc.start(option.canvas,true);
                }
            },
            mon2(option){
                let a = option;
                // jc.start(option.canvas,true);
                let img = new Image();
                img.src = './static/images/monster2.png';
                img.onload = function (){
                    jc.start(a.canvas);
                    jc.image(img,-100,-100,109,113).id(a.id).level(2);
                    jc.start(option.canvas,true);
                }
            },
            mon3(option){
                let a = option;
                // jc.start(option.canvas,true);
                let img = new Image();
                img.src = './static/images/monster3.png';
                img.onload = function (){
                    jc.start(a.canvas);
                    jc.image(img,-100,-100,107,129).id(a.id).level(2);
                    jc.start(option.canvas,true);
                }
            },
            mon4(option){
                let a = option;
                // jc.start(option.canvas,true);
                let img = new Image();
                img.src = './static/images/monster4.png';
                img.onload = function (){
                    jc.start(a.canvas);
                    jc.image(img,-100,-100,125,110).id(a.id).level(2);
                    jc.start(option.canvas,true);
                }
            },
        };
        gameCavans.addEventListener('touchstart',(ex) => {
            let es = ex || event;
            let e = {
                x:es.changedTouches[0].pageX,
                y:es.changedTouches[0].pageY,
            }
            if(this.monsters.length > 0){
                let flag = true;
                this.monsters.forEach(item => {
                    if(flag){
                        if((e.x > item._x && e.x < (item._x + item._width)) && (e.y > item._y && e.y < (item._y + item._height))){
                            item._die();
                        }
                    }
                })
            }
        })
        this.creatNum(1);
    },
    creatNum (num) {
        for(let i = 0;i < num;i++){
            this.arrNum.push(num);
        }
        if(num++ < 30){
            this.creatNum(num);
        } else{ 
            this.creatMonster();
        }
    },
    creatMonster(){
        for(let i = 0;i < this.arrNum[this.chapterIndex];i++){
            let random = Math.floor(Math.random() * 4) + 1;
            this.monster['mon' + random]({canvas: 'game',id: 'mon' + i + this.chapterIndex});
            this.initMonsterMove({obj:'mon' + i + this.chapterIndex,type:random});
        }
    },
    initMonsterMove(option){
        let changeNum = ((this.startMaxX - this.startMinX) + (this.startMaxY - this.startMinY)) * 2 / (this.roundT / this.speed);
        setTimeout(() => {
            let monster = jc('#' + option.obj);
            let _this = this;
            let xy = this.getRandomXY();
            monster._type = option.type;
            monster.__x = xy.x;
            monster.__y = xy.y;
            monster._x = xy.x;
            monster._y = xy.y;
            monster._changeNum = changeNum;//每次更新改变的位置大小
            monster._R = 300;//转动的半径
            monster._Ang = 200;//转动的角度
            monster._run = function (obj) {
                // this.__x = obj.getRandomXY().x;
                // this.__y= obj.getRandomXY().y;
                if(this.__x <= obj.startMaxX && this.__y== obj.startMinY && this.__x > obj.startMinX){
                    this.__x = this.__x - this._changeNum;
                    if(this.__x < obj.startMinX) this.__x = obj.startMinX;
                } else if(this.__x == obj.startMinX && this.__y< obj.startMaxY){
                    this.__y= this.__y+ this._changeNum;
                    if(this.__y> obj.startMaxY) this.__y= obj.startMaxY;
                } else if(this.__x < obj.startMaxX && this.__y== obj.startMaxY){
                    this.__x = this.__x + this._changeNum;
                    if(this.__x > obj.startMaxX) this.__x = obj.startMaxX;
                } else if(this.__y<= obj.startMaxY && this.__x == obj.startMaxX){
                    this.__y= this.__y- this._changeNum;
                    if(this.__y< obj.startMinY) this.__y= obj.startMinY;
                }
                this._Ang = this._Ang + 3;
                // console.log(this._Ang)
                let x = this.__x - this._R * Math.cos(this._Ang * Math.PI / 180);
                let y = this.__y - this._R * Math.sin(this._Ang * Math.PI / 180);
                // console.log(this)
                monster.animate({x:x,y:y},1);
                // obj.monsters.forEach((item,index) => {
                //     if(item.optns.id == option.obj){
                //         obj.monsters[index] = monster;
                //     }
                // });
            };
            monster._runTnterval = setInterval(() => {
                monster._run(_this);
            },this.speed);
            monster._die = function (obj) {
                clearInterval(this._runTnterval);
                this.del();
                let dieImgSrc = '';
                let width = 0,height = 0;
                switch (this._type) {
                    case 1:
                        dieImgSrc = './static/images/monster11.png';
                        width = 180,height = 138;
                        break;
                    case 2:
                        dieImgSrc = './static/images/monster12.png';
                        width = 146,height = 84;
                        break;
                    case 3:
                        dieImgSrc = './static/images/monster13.png';
                        width = 103,height = 94;
                        break;
                    case 4:
                        dieImgSrc = './static/images/monster14.png';
                        width = 92,height = 107;
                        break;
                    default:
                        break;
                };
                let img = new Image();
                img.src = dieImgSrc;
                img.onload = () => {
                    jc.start('game');
                    jc.image(img,this._x,this._y,width,height).id('die' + this.optns.id).level(1);
                    jc.start('game',true);
                };
                setTimeout(() => {
                    let timeDie = 2000,cycle = 30,opacity = 0;
                    let dieInterval = setInterval(() => {
                        opacity += 1/(2000/30);
                        if(opacity >= 1){
                            clearInterval(dieInterval);
                            jc('#' + 'die' + this.optns.id).del();
                        } else{
                            jc('#' + 'die' + this.optns.id).opacity(1 - opacity);
                        }
                    },30);
                }, 200);
            };
            this.monsters.push(monster);
            // console.log(this.monsters);
            
        }, 500);
    },
    getRandomXY(){
        let random = Math.floor(Math.random() * 4) + 1;
        let x = 0, y = 0;
        switch (random) { 
            case 1:
                y = this.startMinY;
                x = Math.random() * (this.startMaxX - this.startMinX) + 50;
                break;
            case 2:
                y = Math.random() * (this.startMaxY - this.startMinY) + 50;
                x = this.startMinX;
                break;
            case 3:
                y = this.startMaxY;
                x = Math.random() * (this.startMaxX - this.startMinX) + 50;
                break;
            case 4:
                y = Math.random() * (this.startMaxY - this.startMinY) + 50;
                x = this.startMaxX;
                break;
            default:
                break;
        }
        return {
            x:x,
            y:y
        }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
@import "../common/styles/animation.less";
section {
  width: 100%;
  height: 100%;
  position: relative;
}
.sectionOne {
  height: 100vh;
  background-color: #2d4081;
  .pageOne {
    width: 429px;
    height: 429px;
    border: 1px solid #1f2b57;
    background-color: #233264;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    border-radius: 50%;
    box-shadow: 0px 0px 113px #283972, 0px 0px 0px 114px #223161,
      0px 0px 0px 193px #2a3d79, 0px 0px 0px 194px #273971,
      0px 0px 0px 254px #2c3f7e, 0px 0px 0px 255px #2a3c79;
  }
  .bg-star {
    width: 100%;
    height: 100%;
    background: url(../assets/star_bg.png) no-repeat;
    background-size: cover;
    position: absolute;
  }
  .canvas {
      position: absolute;
      z-index: 10;
  }
  .typeBar {
    width: 100%;
    height: 73px;
    position: absolute;
    background-color: rgba(0, 0, 0, 0.4);
    bottom: 0;
    left: 0;
    .score {
      width: 263px;
      height: 100%;
      float: left;
      background: url(../assets/monster4.png) 23px 18px no-repeat;
      background-size: 59px 51px;
      text-indent: 82px;
      line-height: 73px;
      font-size: 30px;
      color: #55b9f8;
    }
    .time {
      width: 180px;
      height: 100%;
      float: left;
      background: url(../assets/time.png) 0px 16px no-repeat;
      text-indent: 56px;
      line-height: 73px;
      font-size: 30px;
      color: #ffe400;
    }
  }
}
.music {
  width: 50px;
  height: 47px;
  position: absolute;
  bottom: 10px;
  right: 10px;
  z-index: 1000; 
  &.run {
    background: url(../assets/run.png) no-repeat;
    animation: run 3s;
    animation-timing-function: linear;
    animation-iteration-count: infinite;
  }
  &.stop {
    background: url(../assets/stop.png) no-repeat;
  }
}
</style>
