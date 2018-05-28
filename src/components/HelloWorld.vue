<template>
  <div>
      <section class="sectionOne">
          <div class="pageOne"></div>
      </section>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      msg: 'Welcome to Your Vue.js App',
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
    
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
    }
    
</style>
