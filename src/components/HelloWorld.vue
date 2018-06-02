<template>
  <div>
      <section class="sectionOne" id="sectionOne" :class="{'sectionOneIn':step == 1,'sectionOneOut':step == 2,}">
          <div class="pageOne"></div>
          <div class="bg-star"></div>
          <div class="main-page">
              <div class="rocket"></div>
              <div class="thanks"></div>
              <div class="withYou"></div>
              <div class="miaov"></div>
          </div>
          <div class="arrow"></div>
          <div class="process" v-if="!imgLoadingEnd">
              <span></span>
              <em></em>
          </div>
          <!-- <img src="../assets/star_bg.png" alt=""> -->
      </section>
      <div class="music" :class="{'run':musicState,'stop':!musicState}" @click="toggleVideo">
          <audio src="../../static/video/bg.mp3" id="music" preload="auto"></audio>
      </div>
  </div>
</template>

<script>
import Vue from "vue";
export default {
  name: 'HelloWorld',
  data() {
    return {
      msg: 'Welcome to Your Vue.js App',
      step:1,
      musicState:false,//音乐播放状态
      music:{},//音乐元素对象
      imgLoadingEnd:false,//图片资源加载标识
    };
  },
  mounted:function(){
      this.loading();
    //   setTimeout(() => {
    //       this.step = 2;
    //   }, 2000);
      Vue.nextTick(() => {
        this.music = document.getElementById('music');
        this.initEvent();
      });
  },
  methods:{
      initEvent(){
          let sectionOne = document.getElementById('sectionOne');
          let sectionOneTouchY = 0;
          sectionOne.addEventListener('touchstart',(e) => {
              sectionOneTouchY = e.touches[0].pageY;
          });
          sectionOne.addEventListener('touchend',(e) => {
              let sectionOneTouchEndY = e.changedTouches[0].pageY;
              if(sectionOneTouchEndY < sectionOneTouchY){
                  this.step = 2;
              }
              
          })
      },
      toggleVideo(){
          if(!this.music) return;
          if(this.music.paused){
              this.music.play();
              this.musicState = true;
          } else{
              this.music.pause();
              this.musicState = false;
          }
      },
      loading(){
          let imgArr = [
              './static/images/star_bg.png',
            //   '../assets/rocket.png',
            //   '../assets/thanks.png',
            //   '../assets/widthyou.png',
            //   '../assets/miaov.png',
            //   '../assets/arrow2.png',
            //   '../assets/run.png',
            //   '../assets/stop.png'
          ];
          let imgNum = 0;
          imgArr.forEach(item => {
              let img = document.createElement('img');
              img.src = item;
              img.onload = () => {
                  imgNum ++;
                  if(imgNum == imgArr.length){
                      this.imgLoadingEnd = true;
                  }
              }
          })
      }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
    @import '../common/styles/animation.less';
    section{
        width: 100%;
        height: 100%;
        position: relative;
    }
    .sectionOne{
        height: 100vh;
        background-color: #2d4081;
        .pageOne{
            width: 429px;
            height: 429px;
            border: 1px solid #1f2b57;
            background-color: #233264;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%,-50%);
            border-radius: 50%;
            box-shadow: 0px 0px 113px #283972,0px 0px 0px 114px #223161,0px 0px 0px 193px #2a3d79,0px 0px 0px 194px #273971,0px 0px 0px 254px #2c3f7e,0px 0px 0px 255px #2a3c79;
        }
        .bg-star{
            width: 100%;
            height: 100%;
            background: url(../assets/star.png) no-repeat;
            background-size: cover;
            position: absolute;
        }
        .main-page{
            width: 429px;
            height: 429px;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%,-50%);
            .rocket{
                width: 165px;
                height: 195px;
                position: absolute;
                top: -29px;
                left: 48px;
                background: url(../assets/rocket.png) no-repeat;
            }
            .thanks{
                width: 399px;
                height: 228px;
                position: absolute;
                top: 43px;
                left: 38px;
                opacity: 0;
                background: url(../assets/thanks.png) no-repeat;
            }
            .withYou{
                width: 364px;
                height: 134px;
                position: absolute;
                top: 208px;
                left: 48px;
                opacity: 0;
                background: url(../assets/widthyou.png) no-repeat;
            }
            .miaov{
                width: 193px;
                height: 82px;
                position: absolute;
                top: -3px;
                left: 209px;
                opacity: 0;
                background: url(../assets/miaov.png) no-repeat;
            }
        }
        .arrow{
            width: 90px;
            height: 65px;
            position: absolute;
            left: 50%;
            transform: translate(-50%);
            bottom: 40px;
            background: url(../assets/arrow2.png) no-repeat;
        }
       
        &.sectionOneIn{
            .main-page{
                .rocket{
                    animation: rocketIn 1s;
                    animation-fill-mode: forwards;
                }
                .thanks{
                    animation: thanksIn 1s;
                    animation-delay: 1s;
                    animation-fill-mode: forwards;
                }
                .withYou{
                    animation: thanksIn 1s;
                    animation-delay: .8s;
                    animation-fill-mode: forwards;
                }
                .miaov{
                    animation: miaovIn 1s;
                    animation-delay: .8s;
                    animation-fill-mode: forwards;
                }
            }
            .arrow{
                animation: arrow 2s;
                animation-iteration-count: infinite;
            }
        }
        &.sectionOneOut{
            .pageOne{
                animation: circleOut 1s;
                animation-fill-mode: forwards;
            }
            .bg-star{
                animation: starOut 1s;
                animation-fill-mode: forwards;
            }
            .main-page{
                .rocket{
                    animation: rocketOut 1s;
                    animation-fill-mode: forwards;
                }
                .thanks{
                    animation: thanksOut 1s;
                    animation-delay: 0.1s;
                    animation-fill-mode: forwards;
                }
                .withYou{
                    animation: thanksOut 1s;
                    animation-delay: 0.15s;
                    animation-fill-mode: forwards;
                }
                .miaov{
                    animation: miaovOut 1s;
                    animation-delay: .8s;
                    animation-fill-mode: forwards;
                }
            }
            .arrow{
                animation: arrow 2s;
                animation-iteration-count: infinite;
            }
        }
         
    }
    .music{
        width: 50px;
        height: 47px;
        position: absolute;
        bottom: 10px;
        right: 10px;
        &.run{
            background: url(../assets/run.png) no-repeat;
            animation: run 3s;
            animation-timing-function: linear;
            animation-iteration-count: infinite;
        }
        &.stop{
            background: url(../assets/stop.png) no-repeat;
        }
    }
    .process{
        width: 463px;
        height: 28px;
        border: 1px solid #4091dc;
        border-radius: 14px;
        position: absolute;
        bottom: 119px;
        left: 50%;
        margin-left: -231px;
        overflow: hidden;
        span{
            width: 0;
            height: 28px;
            display: block;
            background-color: #4091dc;
            width: 1%;
        }
        em{
            position: absolute;
            top: 50px;
            width: 100%;
            display: block;
            text-align: center;
            font-size: 12px;
            color: #fff;
        }
    }
</style>
