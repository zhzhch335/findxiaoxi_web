<template>
  <div id="playGroud">
    <audio ref="music" :src="musicSrc" loop></audio>
    <h1 v-if="level !== '' && showHappy == false">{{LEVEL_NAME[level]}}</h1>
    <div class="levelContent">
      <div v-if="level === ''">加载中，请稍候……</div>
      <!-- 序章 -->
      <div v-if="level === 0">
        <div>
          突然有一天，<span style="text-decoration:line-through;">大家的爹</span>小希失踪了，快快移动你的鼠标，在各处寻找小希吧！
        </div>
        <div style="margin-top: 20px;">
          （点击下方图片开始）
        </div>
        <div style="margin-top: 10px;font-size: 10px;color:#333">
          （据说笨蛋在5分钟之内无法通关）
        </div>
        <div>
          <img @click="nextLevelFirst()" style="cursor:pointer;margin-top: 100px;width: 20%" src="./assets/xiaoxi/alert.gif"
            alt="">
        </div>
      </div>
      <!-- 第一关 -->
      <div v-if="level === 1">
        <div style="font-size: 20px;">请点击屏幕上的<span @click="showLevel1Img">小希</span>进入下一关</div>
      </div>
      <!-- 第二关 -->
      <div v-if="level === 2">
        <div>你管小希叫什么</div>
        <input :disabled="level2InputDisabled" @input="checkLevel2Input" v-model="level2Input" autofocus class="xiInput" type="text" />
      </div>
      <!-- 第三关 -->
      <div v-if="level === 3">
        <div class="level3Text" style="display: flex;align-items: center">给小希发红包，发<input class="level3Input"
            @input="checkLevel3Input" v-model="level3Input" />元就够了</div>
      </div>
      <!-- 第四关 -->
      <div v-if="level === 4">
        <div>小希在独播，请拿走一样东西避免直播事故：</div>
        <div>
          <img usemap="#coke" style="width:300px;margin-top:10px" src="./assets/xiaoxi/level4.jpg" alt="">
          <map name="coke">
            <area shape="rect" coords="260,0,300,50" alt="Sun" @click="levelImg=true">
          </map>
        </div>
      </div>
      <!-- 第五关 -->
      <div v-if="level === 5">
        <div>这关删了，直接点下一关</div>
        <img v-if="!levelImg" style="width: 100px; margin-top: 20px;" src="./assets/xiaoxi/confusion.gif" alt="">
        <img v-if="levelImg" style="width: 100px; margin-top: 20px;" src="./assets/xiaoxi/love.png" alt="">
      </div>
      <!-- 第六关 -->
      <div v-if="level === 6">
        <div>见到小希后，帅老DD们往往会在心里默念十个字</div>
        <div>让你的电脑把这十个字默默记在心里，然后点击下面的按钮检测他的内心</div>
        <button class="checkBtn" style="margin: 20px 0;" @click="checkLevel6Clipboard">检测</button>
        <div :class="shakeIt?'shake':''" v-if="level6False && !levelImg">不对哦，给你一拳!</div>
        <input type="text" placeholder="这是一个没什么用的输入框" style="position:absolute;right: 50px;bottom: 50px;">
      </div>
      <!-- 第七关 -->
      <div v-if="level === 7">
        <div>这关删了，直接点下一关</div>
      </div>
      <!-- 第八关 -->
      <div v-if="level === 8">
        <div>这关删了，直接点下一关</div>
      </div>
      <!-- 第九关 -->
      <div v-if="level === 9">
        <div>这关删了，直接点下一关</div>
      </div>
      <!-- 第十关 -->
      <div v-if="level === 10">
        <div v-if="!showHappy">吹灭蜡烛吧</div>
        <div v-if="!showHappy" :class="['wrapper', level6Blow ? '': 'stop']" @click="blow()" class="wrapper">
          <div class="candles">
            <div class="candle1">
              <div class="candle1__body">
                <div class="candle1__eyes"><span class="candle1__eyes-one"></span><span
                    class="candle1__eyes-two"></span></div>
                <div class="candle1__mouth"></div>
              </div>
              <div class="candle1__stick"></div>
            </div>
            <div class="candle2">
              <div class="candle2__body">
                <div class="candle2__eyes">
                  <div class="candle2__eyes-one"></div>
                  <div class="candle2__eyes-two"></div>
                </div>
              </div>
              <div class="candle2__stick"></div>
            </div>
            <div class="candle2__fire"></div>
            <div class="sparkles-one"></div>
            <div class="sparkles-two"></div>
            <div class="candle__smoke-one"></div>
            <div class="candle__smoke-two"></div>
          </div>
          <div class="floor"></div>
        </div>
        <div v-if="!showHappy" style="width: 100%; height: 300px"></div>
        <div v-if="showHappy"  style="height: 500px;overflow:hidden;display: flex;align-items: center;">
          <!-- <div> -->
            <div class="blessing" style="white-space: pre;">{{blessing}}
              <br>
<button :style="{
  visibility: showStaff?'visible':'hidden'
}" class="checkBtn" style="margin: 20px 0;font-size: 20px;" @click="reset()">重玩</button></div>
            <div v-html="`制作人：琛琛
页面设计：不忘那抹天蓝 Sicro 
创意协力：XX XX XX XX 
XX XX XX XX XX XX XX
测试协力： XX XX XX XX 
XX XX XX XX XX XX 
题目协力：XX XX XX XX XX
XX XX XX XX XX XX
鸣谢小团体：鱼群、虚研村游戏群`" v-if="showStaff" class="staff">
            </div>
          <!-- </div> -->
        </div>
      </div>
      <!-- 通关按钮 -->
      <div>
        <img v-if="levelImg" @click="nextLevel()" style="cursor:pointer;margin-top: 100px;width:80px"
          src="./assets/xiaoxi/alert.gif" alt="">
      </div>
      <!-- 提示按钮 -->
      <div @click="showTips()" class="tips" v-if="tipsShow">
        <img src="./assets/xiaoxi/gold.gif"/>
        <div>来点提示吧</div>
      </div>
    </div>
  </div>
</template>

<script>
  const LEVEL_NAME = [
    "序章",
    "第一关",
    "第二关",
    "第三关",
    "第四关",
    "第五关",
    "第六关",
    "第七关",
    "第八关",
    "第九关",
    "第十关",
  ]
  const LEVEL_TIPS = [
    "你是笨蛋吗，直接点图片就好了啊~",
    "点击屏幕上的“小希”",
    "希爹是我爹我是希爹儿~",
    "某次直播期间，希爹收到社长0.66元的高额红包",
    "XXXXX，你罪大恶极！我____撒了！",
    "让这个窗口失去焦点十秒就好了",
    "十个字你肯定知道了，复制到剪贴板里再检查~",
    "上面的菜单栏你从来没点开过吧",
    "你右下角🗪微信响了哦，哦不是微信，那是啥？（其实就是图标在右下角托盘中啦~）",
    "关闭窗口，我会挽留你的",
    "点一下蜡烛就好啦~"
  ]
export default {
  name: 'App',
    data() { return {
      musicSrc: require("./assets/bgm.mp3"),

      level: "",
      LEVEL_NAME,
      LEVEL_TIPS,
      levelTime: 0,
      tipsShow: false,
      timeIntervalId: null,

      // 第一关
      levelImg: false,

      // 第二关
      level2Input: "",
      level2InputDisabled: false,

      // 第三关
      level3Input: "",

      // 第六关
      level6False: false,
      shakeIt: false,

      // 第十关
      level6Blow: false,
      showHappy: false,
      blessing: "",
      showStaff: false,

      direction: window.orientation
    }
    },
    watch: {
      level: {
        handler(nval) {
          clearInterval(this.timeIntervalId)
          this.tipsShow = false
          this.levelTime = 0
          this.timeIntervalId = setInterval(() => {
            this.levelTime++
            if (this.levelTime >= 10) {
              clearInterval(this.timeIntervalId);
              this.tipsShow = true
              this.levelTime = 0
            }
          },1000)
          if (nval == 5 || nval == 7 || nval == 8 || nval == 9) {
            this.levelImg = true
          }
        }
      },
      immediate: true
    },
    methods: {
      showTips() {
        alert(this.LEVEL_TIPS[this.level])
      },

      reset() {
        this.showHappy = false;
        this.level = 0;

        // 第一关
        this.levelImg = false;

        // 第二关
        this.level2Input = "";
        this.level2InputDisabled = false;

        // 第三关
        this.level3Input = "";

        // 第六关
        this.level6False = false;
        this.shakeIt = false;

        // 第十关
        this.level6Blow = false;
        this.showHappy = false;
        this.blessing = "";
        this.showStaff = false;

        location.hash = this.level;
      },

      nextLevelFirst() {
        this.$refs.music.volume = 0.2;
        this.$refs.music.play();

        this.nextLevel();
      },
      nextLevel() {
        this.levelImg = false
        this.level++
        location.hash = this.level
      },

      // 第一关
      showLevel1Img() {
        this.levelImg = true
      },

      // 第二关
      checkLevel2Input() {
        if (this.level2Input.includes("爹")) {
          this.level2InputDisabled = true;
          this.levelImg = true;
        }
      },

      // 第三关
      checkLevel3Input() {
        if (this.level3Input == 0.66) {
          this.levelImg = true;
        }
      },

      // 第六关
      async checkLevel6Clipboard() {
        setTimeout(async () => {
          try {
            const currentClipboard = await navigator.clipboard.readText();
            if (currentClipboard == "希爹是我爹我是希爹儿" || currentClipboard == "希爹是我爹，我是希爹儿" || currentClipboard == "希爹是我爹,我是希爹儿" || currentClipboard == "希爹是我爹 我是希爹儿") {
              this.levelImg = true;
            }
            else {
              this.level6False = true;
              this.shakeIt = false;
              setTimeout(() => {
                this.shakeIt = true;
              }, 500)
            }
          }
          catch(e) {
            console.log(e);
            alert("求求你，给一下剪贴板权限好不好~，嘛，不给就算了，下一关吧")
            this.levelImg = true;
          }
        }, 1000);
      },

      // 第十关
      blow() {
        // 避免二次点击打印机乱了
        if (this.level6Blow) return
        clearInterval(this.timeIntervalId)
        this.tipsShow = false;
        this.level6Blow = true;
        setTimeout(() => {
          this.showHappy = true;
          this.$forceUpdate();
          const allBlessing = `以前每次制作二创的时候，总想着煽情，想唤起点回忆和眼泪
现在想想，过生日嘛，开开心心得多好
眼泪是一种释放，开开心心地大笑不也是一种释放嘛
毕竟我们喜欢的是那个乐观阳光
（喜欢和帅老DD对线）的小希啊
小希，生日快乐~
愿你一直阳光，即使阴天也会成为自己的太阳`.split("");
          let id = setInterval(() => {
            if (allBlessing.length) {
              this.blessing += allBlessing.splice(0,1)
            }
            else {
              clearInterval(id);
              this.showStaff = true
            }
          }, 200)
        }, 2500)
      }
    },
    mounted() {
      this.level = Number(location.hash.replace("#", "")) || 0;

      // if (window.innerHeight > window.innerWidth) {
      //   document.getElementsByTagName("html")[0].style.transform = "rotate(90deg)"
      // }
    }
}
</script>

<style>
  @font-face {
    font-family: "yunduo";
    src: url("./assets/JinTianYunDuoYouDianTian-2.ttf");
  }

  html {
    background: #8ea1d0;  /* fallback for old browsers */
    background: -webkit-linear-gradient(to left, #8ea1d0, #fff);  /* Chrome 10-25, Safari 5.1-6 */
    background: linear-gradient(to left, #8ea1d0, #fff); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
  }

  html,
  body {
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
    font-family: "yunduo";
    height: 100%;
    margin: 0;
    padding: 0;
    overflow: hidden;
    font-size: 20px;
    
  }

  #playGroud {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    color: black;
    margin: auto;
  }

  @media screen and (orientation: portrait) {
    #playGroud {
        height: 67.5vw;
        width: 90vw;
        font-size: 15px;
    }
    .level3Text {
      font-size: 20px;
    }
  }

  @media screen and (orientation: landscape) {
      #playGroud {
        height: 100vh;
        width: 133vh;
      }
      .level3Text {
        font-size: 30px;
      }
  }



  .xiInput {
    height: 40px;font-size: 25px;text-align: center;border:none;outline:none;background: url('./assets/xiaoxi/input.png');background-size: 100% 100%;margin-top: 20px;
  }

  .level3Input {
    height: 30px;
    border: none;
    border-bottom: solid #000 1px;
    width: 80px;
    margin: 0 10px;
    font-size: 30px;
    background: transparent;
  }

  .level3Input:focus-visible {
    border: none;
    outline: none;
    border-bottom: solid #333 1px;
  }

  .shake {
    animation: shake 800ms ease-in-out;
  }

  @keyframes shake {

    /* 水平抖动，核心代码 */
    0%,
    100% {
      transform: translate3d(-1px, 0, 0);
    }

    20%,
    80% {
      transform: translate3d(+2px, 0, 0);
    }

    30%,
    70% {
      transform: translate3d(-4px, 0, 0);
    }

    40%,
    60% {
      transform: translate3d(+4px, 0, 0);
    }

    50% {
      transform: translate3d(-4px, 0, 0);
    }
  }

  .checkBtn {
    border: solid 2px #fff;
    -webkit-align-self: center;
    -ms-flex-item-align: center;
    align-self: center;
    background: transparent;
    padding: 1rem 1rem;
    margin: 0 1rem;
    -webkit-transition: all .5s ease;
    transition: all .5s ease;
    color: #fff;
    font-size: 2rem;
    letter-spacing: 1px;
    outline: none;
    box-shadow: 20px 38px 34px -26px rgba(0, 0, 0, 0.2);
    border-radius: 255px 15px 225px 15px/15px 225px 15px 255px;
    font-family: "yunduo";
    cursor: pointer;
  }
  .checkBtn:hover {
    box-shadow: 2px 8px 4px -6px rgba(0, 0, 0, 0.3);
  }
  .staff {
    white-space: pre;
    position: fixed;
    font-size: 15px;
    right: 20px;
    bottom: 20px;
  }
  .scrollContent {
    animation: marquee 4s linear forwards;
  }
  @keyframes marquee {
    from {
      transform: translateY(0);
    }
    to {
      transform: translateY(-190px);
    }
  }
  .tips {
    position: fixed;
    right: 20px;
    top: 20px;
    cursor: pointer;
    font-size: 15px;
  }
  .tips img {
    width: 50px;
  }



  /* 吹蜡烛 */
/* body {
  background-color: #FEF4AD;
  animation: change-background 3s linear;
} */

.stop * {
  animation-play-state:paused !important;
}

.wrapper {
  position: absolute;
  left: 50%;
  top: 70%;
  transform: scale(1.5, 1.5) translate(-50%, -50%);
}

.floor {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 350px;
  height: 5px;
  background: #673C63;
  transform: translate(-50%, -50%);
  box-shadow: 0px 2px 5px #111;
  z-index: 2;
}

.candles {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 250px;
  height: 150px;
  transform: translate(-50%, -100%);
  z-index: 1;
}

.candle1 {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 35px;
  height: 100px;
  background: #fff;
  border: 3px solid #673C63;
  border-bottom: 0px;
  border-radius: 3px;
  transform-origin: center right;
  transform: translate(60%, -25%);
  box-shadow: -2px 0px 0px #95c6f2 inset;
  animation: expand-body 3s linear;
}

.candle1__stick, .candle2__stick {
  position: absolute;
  left: 50%;
  top: 0%;
  width: 3px;
  height: 15px;
  background: #673C63;
  border-radius: 8px;
  transform: translate(-50%, -100%);
}

.candle2__stick {
  height: 12px;
  transform-origin: bottom center;
  animation: stick-animation 3s linear;
}

.candle1__eyes, .candle2__eyes {
  position: absolute;
  left: 50%;
  top: 0%;
  width: 35px;
  height: 30px;
  transform: translate(-50%, 0%);
}

.candle1__eyes-one {
  position: absolute;
  left: 30%;
  top: 20%;
  width: 5px;
  height: 5px;
  border-radius: 100%;
  background: #673C63;
  transform: translate(-70%, 0%);
  animation: blink-eyes 3s linear;
}

.candle1__eyes-two {
  position: absolute;
  left: 70%;
  top: 20%;
  width: 5px;
  height: 5px;
  border-radius: 100%;
  background: #673C63;
  transform: translate(-70%, 0%);
  animation: blink-eyes 3s linear;
}

.candle1__mouth {
  position: absolute;
  left: 40%;
  top: 20%;
  width: 0px;
  height: 0px;
  border-radius: 20px;
  background: #673C63;
  transform: translate(-50%, -50%);
  animation: uff 3s linear;
}

.candle__smoke-one {
  position: absolute;
  left: 30%;
  top: 50%;
  width: 30px;
  height: 3px;
  background: grey;
  transform: translate(-50%, -50%);
  animation: move-left 3s linear;
}

.candle__smoke-two {
  position: absolute;
  left: 30%;
  top: 40%;
  width: 10px;
  height: 10px;
  border-radius: 10px;
  background: grey;
  transform: translate(-50%, -50%);
  animation: move-top 3s linear;
}

.candle2 {
  position: absolute;
  left: 20%;
  top: 65%;
  width: 42px;
  height: 60px;
  background: #fff;
  border: 3px solid #673C63;
  border-bottom: 0px;
  border-radius: 3px;
  transform: translate(60%, -15%);
  transform-origin: center right;
  box-shadow: -2px 0px 0px #95c6f2 inset;
  animation: shake-left 3s linear;
}

.candle2__eyes-one {
  position: absolute;
  left: 30%;
  top: 50%;
  width: 5px;
  height: 5px;
  display: inline-block;
  border: 0px solid #673C63;
  border-radius: 100%;
  float: left;
  background: #673C63;
  transform: translate(-80%, 0%);
  animation: changeto-lower 3s linear;
}

.candle2__eyes-two {
  position: absolute;
  left: 70%;
  top: 50%;
  width: 5px;
  height: 5px;
  display: inline-block;
  border: 0px solid #673C63;
  border-radius: 100%;
  float: left;
  background: #673C63;
  transform: translate(-80%, 0%);
  animation: changeto-greater 3s linear;
}

.light__wave {
  position: absolute;
  top: 35%;
  left: 35%;
  width: 75px;
  height: 75px;
  border-radius: 100%;
  z-index: 0;
  transform: translate(-25%, -50%) scale(2.5, 2.5);
  border: 2px solid rgba(255, 255, 255, 0.2);
  animation: expand-light 3s linear;
}

.candle2__fire {
  position: absolute;
  top: 50%;
  left: 40%;
  display: block;
  width: 16px;
  height: 20px;
  background-color: red;
  border-radius: 50% 50% 50% 50% / 60% 60% 40% 40%;
  background: #FF9800;
  transform: translate(-50%, -50%);
  animation: dance-fire 3s linear;
}

@keyframes blink-eyes {
  0%,35% {
      opacity: 1;
      transform: translate(-70%, 0%);
  }

  36%,39% {
      opacity: 0;
      transform: translate(-70%, 0%);
  }

  40% {
      opacity: 1;
      transform: translate(-70%, 0%);
  }

  50%,65% {
      transform: translate(-140%, 0%);
  }

  66% {
      transform: translate(-70%, 0%);
  }
}

@keyframes expand-body {
  0%,40% {
      transform: scale(1, 1) translate(60%, -25%);
  }

  45%,55% {
      transform: scale(1.1, 1.1) translate(60%, -28%);
  }

  60% {
      transform: scale(0.89, 0.89) translate(60%, -25%);
  }

  65% {
      transform: scale(1, 1) translate(60%, -25%);
  }

  70% {
      transform: scale(0.95, 0.95) translate(60%, -25%);
  }

  75% {
      transform: scale(1, 1) translate(60%, -25%);
  }
}

@keyframes uff {
  0%,40% {
      width: 0px;
      height: 0px;
  }

  50%,54% {
      width: 15px;
      height: 15px;
      left: 30%;
  }

  59% {
      width: 5px;
      height: 5px;
      left: 20%;
  }

  62% {
      width: 2px;
      height: 2px;
      left: 20%;
  }

  67% {
      width: 0px;
      height: 0px;
      left: 30%;
  }
}

@keyframes change-background {
  0%,59%,98%,100% {
      background: #FEF4AD;
  }

  61%,97% {
      background: #F8AE39;
  }
}

@keyframes move-left {
  0%,59%,100% {
      width: 0px;
      left: 40%;
  }

  60% {
      width: 30px;
      left: 30%;
  }

  68% {
      width: 0px;
      left: 20%;
  }
}

@keyframes move-top {
  0%,64%,100% {
      width: 0px;
      height: 0px;
      top: 0%;
  }

  65% {
      width: 10px;
      height: 10px;
      top: 40%;
      left: 40%;
  }

  80% {
      width: 0px;
      height: 0px;
      top: 20%;
  }
}

@keyframes shake-left {
  0%,40% {
      left: 20%;
      transform: translate(60%, -15%);
  }

  50%,54% {
      left: 20%;
      transform: translate(60%, -15%);
  }

  59% {
      left: 20%;
      transform: translate(60%, -15%);
  }

  62% {
      left: 18%;
      transform: translate(60%, -15%);
  }

  65% {
      left: 21%;
      transform: translate(60%, -15%);
  }

  67% {
      left: 20%;
      transform: translate(60%, -15%);
  }

  75% {
      left: 20%;
      transform: scale(1.15, 0.85) translate(60%, -15%);
      background: #fff;
      border-color: #673C63;
  }

  91% {
      left: 20%;
      transform: scale(1.18, 0.82) translate(60%, -10%);
      background: #F44336;
      border-color: #F44336;
      box-shadow: -2px 0px 0px #F44336 inset;
  }

  92% {
      left: 20%;
      transform: scale(0.85, 1.15) translate(60%, -15%);
  }

  95% {
      left: 20%;
      transform: scale(1.05, 0.95) translate(60%, -15%);
  }

  97% {
      left: 20%;
      transform: scale(1, 1) translate(60%, -15%);
  }
}

@keyframes stick-animation {
  0%,40% {
      left: 50%;
      top: 0%;
      transform: translate(-50%, -100%);
  }

  50%,54% {
      left: 50%;
      top: 0%;
      transform: translate(-50%, -100%);
  }

  59% {
      left: 50%;
      top: 0%;
      transform: translate(-50%, -100%);
  }

  62% {
      left: 50%;
      top: 0%;
      transform: rotateZ(-15deg) translate(-50%, -100%);
  }

  65% {
      left: 50%;
      top: 0%;
      transform: rotateZ(15deg) translate(-50%, -100%);
  }

  70% {
      left: 50%;
      top: 0%;
      transform: rotateZ(-5deg) translate(-50%, -100%);
  }

  72% {
      left: 50%;
      top: 0%;
      transform: rotateZ(5deg) translate(-50%, -100%);
  }

  74%,84% {
      left: 50%;
      top: 0%;
      transform: rotateZ(0deg) translate(-50%, -100%);
  }

  85% {
      transform: rotateZ(180deg) translate(0%, 120%);
  }

  92% {
      left: 50%;
      top: 0%;
      transform: translate(-50%, -100%);
  }
}

@keyframes expand-light {
  10%,29%,59%,89% {
      transform: translate(-25%, -50%) scale(0, 0);
      border: 2px solid rgba(255, 255, 255, 0);
  }

  90%,20%,50% {
      transform: translate(-25%, -50%) scale(1, 1);
  }

  95%,96%,26%,27%,56%,57% {
      transform: translate(-25%, -50%) scale(2, 2);
      border: 2px solid rgba(255, 255, 255, 0.5);
  }

  0%,28%,58%,100% {
      transform: translate(-25%, -50%) scale(2.5, 2.5);
      border: 2px solid rgba(255, 255, 255, 0.2);
  }
}

@keyframes dance-fire {
  59%,89% {
      left: 40%;
      width: 0px;
      height: 0px;
  }

  90%,0%,7%,15%,23%,31%,39%,47%,55% {
      left: 40.8%;
      width: 16px;
      height: 20px;
      background: #FFC107;
  }

  94%,3%,11%,19%,27%,35%,43%,51%,58% {
      left: 41.2%;
      width: 16px;
      height: 20px;
      background: #FF9800;
  }
}

@keyframes changeto-lower {
  0%,70%,90% {
      padding: 0px;
      display: inline-block;
      border-radius: 100%;
      background: #673C63;
      border-width: 0 0 0 0;
      border: 0px solid #673C63;
      transform: translate(-90%, 0%);
  }

  71%,89% {
      background: none;
      border: solid #673C63;
      border-radius: 0px;
      border-width: 0 2px 2px 0;
      display: inline-block;
      padding: 1px;
      float: left;
      transform-origin: bottom left;
      transform: rotate(-45deg) translate(-50%, -65%);
      -webkit-transform: rotate(-45deg) translate(-50%, -65%);
  }
}

@keyframes changeto-greater {
  0%,70%,90% {
      top: 50%;
      padding: 0px;
      display: inline-block;
      border-radius: 100%;
      background: #673C63;
      border-width: 0 0 0 0;
      border: 0px solid #673C63;
      transform: translate(-80%, 0%);
  }

  71%,89% {
      top: 30%;
      background: none;
      border: solid #673C63;
      border-radius: 0px;
      border-width: 0 2px 2px 0;
      display: inline-block;
      padding: 1px;
      float: left;
      transform-origin: bottom left;
      transform: rotate(135deg) translate(-80%, 20%);
      -webkit-transform: rotate(135deg) translate(-80%, 20%);
  }
}

</style>
