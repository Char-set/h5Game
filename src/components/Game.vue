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
                    jc.image(img,-100,-100,109,114).id(a.id);
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
                    jc.image(img,-100,-100,109,113).id(a.id);
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
                    jc.image(img,-100,-100,107,129).id(a.id);
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
                    jc.image(img,-100,-100,125,110).id(a.id);
                    jc.start(option.canvas,true);
                }
            },
        };
        // this.monster.mon1({canvas:'game'});
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
            this.initMonsterMove({obj:'mon' + i + this.chapterIndex});
        }
    },
    initMonsterMove(option){
        
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
        let changeNum = ((this.startMaxX - this.startMinX) + (this.startMaxY - this.startMinY)) * 2 / (this.roundT / this.speed);
        setTimeout(() => {
            let monster = jc('#' + option.obj);
            let _this = this;
            monster.__x = x;
            monster.__y = y;
            monster._x = x;
            monster._y = y;
            monster._changeNum = changeNum;
            monster._run = function (obj) {
                console.log(this)
                if(this._x <= obj.startMaxX && this._y == obj.startMinY && this._x > obj.startMinX){
                    this._x = this._x - this._changeNum;
                    if(this._x < obj.startMinX) this._x = obj.startMinX;
                } else if(this._x == obj.startMinX && this._y < obj.startMaxY){
                    this._y = this._y + this._changeNum;
                    if(this._y > obj.startMaxY) this._y = obj.startMaxY;
                } else if(this._x < obj.startMaxX && this._y == obj.startMaxY){
                    this._x = this._x + this._changeNum;
                    if(this._x > obj.startMaxX) this._x = obj.startMaxX;
                } else if(this._y <= obj.startMaxY && this._x == obj.startMaxX){
                    this._y = this._y - this._changeNum;
                    if(this._y < obj.startMinY) this._y = obj.startMinY;
                }
                monster.animate({x:this._x,y:this._y},1);
            };
            setInterval(() => {
                monster._run(_this);
            },this.speed);
        }, 500);
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
