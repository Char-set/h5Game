<template>
  <div :class="{'shake':defeat}">
      <section class="sectionOne" id="sectionOne">
          <div class="pageOne"></div>
          <div class="bg-star"></div>
          <canvas id="game" class="canvas"></canvas>
          <div class="typeBar">
              <div class="score">X{{killNum}}</div>
              <div class="time">{{timeCon.down}}秒</div>
          </div>
      </section>
      <div class="reStart" v-show="showRestart" @click="reStart">重新开始</div>
      <div class="music" :class="{'run':musicState,'stop':!musicState}" @click="toggleVideo">
          <audio src="../../static/video/music.mp3" id="music" preload="auto"></audio>
      </div>
      <div :class="{'bg':defeat}"></div>
  </div>
</template>

<script>
import Vue from "vue";
import JC from "jcscript";
export default {
  name: "Game",
  data() {
    return {
      msg: "Welcome to Your Vue.js App",
      musicState: false, //音乐播放状态
      music: {}, //音乐元素对象
      monster: {}, //怪物控制对象
      arrNum: [], //关卡数组
      time: [], //时间数组
      chapterIndex: 0, //关卡
      startMinX: 50, //最小X位置
      startMinY: 50, //最小Y位置
      startMaxX: window.screen.availWidth + 100, //最大X位置
      startMaxY: window.screen.availHeight + 300, //最大Y位置
      roundT: 20000, //转一圈所需时间
      speed: 18, //速度
      monsters: [], //当前关卡的所有怪物对象,
      dieMonsters: [], //正在死亡的怪物对象
      currentNumber: 0, //生成怪物时，标识当前数量
      killNum: 0, //杀死怪物的数量
      defeat: false, //未杀死怪物
      timeCon: {
        down: 10,
        interval: ""
      }, //时间控制
      showRestart: false //显示重新开始按钮
    };
  },
  mounted: function() {
    Vue.nextTick(() => {
      //获取页面的音乐按钮
      this.music = document.getElementById("music");
      //   初始化游戏页面
      this.initGamePage();
    });
  },
  methods: {
    //   游戏音乐暂停播放控制
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
    // 游戏页面点击，怪物死亡     废弃
    // canvasClick(ev) {
    //   let e = ev || event;
    //   if (this.monsters.length > 0) {
    //     let flag = true;
    //     this.monsters.forEach(item => {
    //       if (flag) {
    //         if (
    //           e.x > item._x &&
    //           e.x < item._x + item._width &&
    //           (e.y > item._y && e.y < item._y + item._height)
    //         ) {
    //           console.log("点钟了");
    //         }
    //       }
    //     });
    //   }
    // },
    // 初始化游戏
    initGamePage() {
      let gameCavans = document.getElementById("game");
      gameCavans.width = document.documentElement.clientWidth;
      gameCavans.height = document.documentElement.clientHeight;
      //   怪物原型，所有生成的怪物类型都储存在这里
      this.monster = {
        mon1(option) {
          let a = option;

          let img = new Image();
          img.src = "./static/images/monster1.png";
          img.onload = function() {
            jc.start(a.canvas);
            jc
              .image(img, -100, -100, 109, 114)
              .id(a.id)
              .level(2);
            jc.start(option.canvas, true);
          };
        },
        mon2(option) {
          let a = option;
          // jc.start(option.canvas,true);
          let img = new Image();
          img.src = "./static/images/monster2.png";
          img.onload = function() {
            jc.start(a.canvas);
            jc
              .image(img, -100, -100, 109, 113)
              .id(a.id)
              .level(2);
            jc.start(option.canvas, true);
          };
        },
        mon3(option) {
          let a = option;
          // jc.start(option.canvas,true);
          let img = new Image();
          img.src = "./static/images/monster3.png";
          img.onload = function() {
            jc.start(a.canvas);
            jc
              .image(img, -100, -100, 107, 129)
              .id(a.id)
              .level(2);
            jc.start(option.canvas, true);
          };
        },
        mon4(option) {
          let a = option;
          // jc.start(option.canvas,true);
          let img = new Image();
          img.src = "./static/images/monster4.png";
          img.onload = function() {
            jc.start(a.canvas);
            jc
              .image(img, -100, -100, 125, 110)
              .id(a.id)
              .level(2);
            jc.start(option.canvas, true);
          };
        }
      };
      //   监听游戏canvas点击（touchstart），点中怪物就死亡
      gameCavans.addEventListener("touchstart", ex => {
        let es = ex || event;
        let e = {
          x: es.changedTouches[0].pageX,
          y: es.changedTouches[0].pageY
        };
        // 若页面上没有怪物，点击事件不做事件处理
        if (this.monsters.length > 0) {
          let flag = true;
          this.monsters.forEach((item, index) => {
            if (flag) {
              // （_x,_y）
              //     --------------
              //    |              |
              //    |              |
              //    |    o(x,y)    | _height
              //    |              |
              //    |              |
              //     --------------
              //          _width
              // 怪物死亡
              if (
                e.x > item._x &&
                e.x < item._x + item._width &&
                (e.y > item._y && e.y < item._y + item._height)
              ) {
                flag = false;
                item._die(this);
                this.monsters.splice(index, 1);
                this.killNum++;
              }
            }
          });
          if (flag) {
            this.defeat = true;
            setTimeout(() => {
              this.defeat = false;
            }, 1000);
          }
          if (this.monsters.length == 0) {
            clearInterval(this.timeCon.interval);
          }
        }
      });
      //   启动游戏，先创建所有关卡
      this.creatNum(1);
    },
    creatNum(num) {
      // 生成的怪物关卡数量数组为 [1,2,2,3,3,3,4,4,4,4] 格式
      for (let i = 0; i < num; i++) {
        this.arrNum.push(num);
      }
      //   最后一关怪物数量30
      if (num++ < 30) {
        this.creatNum(num);
      } else {
        //   开始生成怪物
        this.creatMonster();
      }
    },
    creatMonster() {
      // 根据当前关卡的怪物数量，生成对应数量的怪物
      //   目前四种类型的怪物，随机生成
      let random = Math.floor(Math.random() * 4) + 1;
      let randomStr = new Date().getTime();
      this.monster["mon" + random]({
        canvas: "game",
        id: "mon" + randomStr
      });
      // 初始化怪物的属性（移动，死亡）
      this.initMonsterNature({
        // obj参数为怪物的唯一id，jc插件需要这个在canvas上找到某个怪物
        obj: "mon" + randomStr,
        //   怪物类型标识
        type: random
      });
      this.currentNumber++;
      setTimeout(() => {
        if (this.currentNumber < this.arrNum[this.chapterIndex]) {
          this.creatMonster();
        } else {
          this.currentNumber = 0;
        }
      }, 200);
      this.initTime();
    },
    initTime() {
      if (this.timeCon.interval) {
        clearInterval(this.timeCon.interval);
        this.timeCon.down = 10;
      }
      this.timeCon.interval = setInterval(() => {
        if (this.timeCon.down > 0) {
          this.timeCon.down--;
        } else {
          clearInterval(this.timeCon.interval);
          this.monsters.forEach(item => {
            item._die(this, true);
          });
          this.monsters = [];
          alert("时间到了，一共消灭了" + this.killNum + "只怪物");
          this.showRestart = true;
        }
      }, 1000);
    },
    reStart() {
      this.chapterIndex = 0;
      this.dieMonsters = [];
      this.killNum = 0;
      this.monsters = [];
      this.showRestart = false;
      this.creatMonster();
    },
    initMonsterNature(option) {
      // 计算每次运动改变的位置大小
      let changeNum =
        (this.startMaxX - this.startMinX + (this.startMaxY - this.startMinY)) *
        2 /
        (this.roundT / this.speed);
      // 这里使用延时、异步操作，是因为canvas生成图像需要一定时间，如果直接执行，jc插件可能会找不到刚刚生成的怪物对象
      setTimeout(() => {
        //   当前的怪物对象
        let monster = jc("#" + option.obj);
        let _this = this;
        // 怪物初始位置
        let xy = this.getRandomXY();
        // 把当前怪物类型记录在怪物对象上
        monster._type = option.type;
        // __x,__y,是怪物的圆心位置
        monster.__x = xy.x;
        monster.__y = xy.y;
        // monster._x = xy.x;
        // monster._y = xy.y;
        monster._changeNum = changeNum; //每次更新改变的位置大小
        monster._R = 200 + Math.floor(Math.random() * 100) + 1; //转动的半径
        monster._Ang = 200; //转动的角度
        monster._direction = Math.random() > 0.5 ? -1 : 1;
        monster._run = function(obj) {
          // this.__x = obj.getRandomXY().x;
          // this.__y= obj.getRandomXY().y;
          if (
            this.__x <= obj.startMaxX &&
            this.__y == obj.startMinY &&
            this.__x > obj.startMinX
          ) {
            this.__x = this.__x - this._changeNum;
            if (this.__x < obj.startMinX) this.__x = obj.startMinX;
          } else if (this.__x == obj.startMinX && this.__y < obj.startMaxY) {
            this.__y = this.__y + this._changeNum;
            if (this.__y > obj.startMaxY) this.__y = obj.startMaxY;
          } else if (this.__x < obj.startMaxX && this.__y == obj.startMaxY) {
            this.__x = this.__x + this._changeNum;
            if (this.__x > obj.startMaxX) this.__x = obj.startMaxX;
          } else if (this.__y <= obj.startMaxY && this.__x == obj.startMaxX) {
            this.__y = this.__y - this._changeNum;
            if (this.__y < obj.startMinY) this.__y = obj.startMinY;
          }
          this._Ang = this._Ang + 3 * this._direction;
          // console.log(this._Ang)
          let x = this.__x - this._R * Math.cos(this._Ang * Math.PI / 180);
          let y = this.__y - this._R * Math.sin(this._Ang * Math.PI / 180);
          monster.animate({ x: x, y: y }, 1);
          // obj.monsters.forEach((item,index) => {
          //     if(item.optns.id == option.obj){
          //         obj.monsters[index] = monster;
          //     }
          // });
        };
        monster._runTnterval = setInterval(() => {
          monster._run(_this);
        }, this.speed);
        monster._die = function(vueObj, flag) {
          clearInterval(this._runTnterval);
          this.del();
          let dieImgSrc = "";
          let width = 0,
            height = 0;
          switch (this._type) {
            case 1:
              dieImgSrc = "./static/images/monster11.png";
              (width = 180), (height = 138);
              break;
            case 2:
              dieImgSrc = "./static/images/monster12.png";
              (width = 146), (height = 84);
              break;
            case 3:
              dieImgSrc = "./static/images/monster13.png";
              (width = 103), (height = 94);
              break;
            case 4:
              dieImgSrc = "./static/images/monster14.png";
              (width = 92), (height = 107);
              break;
            default:
              break;
          }
          let img = new Image();
          img.src = dieImgSrc;
          img.onload = () => {
            jc.start("game");
            jc
              .image(img, this._x, this._y, width, height)
              .id("die" + this.optns.id)
              .level(1);
            jc.start("game", true);
          };
          setTimeout(() => {
            let timeDie = 2000,
              cycle = 30,
              opacity = 0,
              dieMonster = jc("#" + "die" + this.optns.id);
            vueObj.dieMonsters.push(dieMonster);
            let dieInterval = setInterval(() => {
              opacity += 1 / (2000 / 30);
              if (opacity >= 1) {
                clearInterval(dieInterval);
                dieMonster.del();
                vueObj.dieMonsters.splice(0, 1);
                if (
                  vueObj.dieMonsters.length == 0 &&
                  vueObj.monsters.length == 0 &&
                  !flag
                ) {
                  vueObj.chapterIndex++;
                  vueObj.creatMonster();
                }
              } else {
                dieMonster.opacity(1 - opacity);
              }
            }, 30);
          }, 200);
        };
        this.monsters.push(monster);
        // console.log(this.monsters);
      }, 500);
    },
    getRandomXY() {
      let random = Math.floor(Math.random() * 4) + 1;
      let x = 0,
        y = 0;
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
        x: x,
        y: y
      };
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
.shake {
  animation: shake 0.4s;
  animation-iteration-count: infinite;
}
.reStart {
  height: 73px;
  position: absolute;
  bottom: 0;
  right: 150px;
  z-index: 1000;
  color: #fff;
  display: inline-block;
  line-height: 73px;
}
.bg {
  width: 100%;
  height: 100%;
  z-index: 9999;
  background: rgba(255, 0, 0, 0.3);
  position: absolute;
  top: 0px;
  left: 0px;
  animation: bg 1s;
  animation-iteration-count: infinite;
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
