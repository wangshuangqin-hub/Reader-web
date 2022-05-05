<template>
  <div class="menu-bar">
    <transition name="slide-up">
      <div class="menu-wrapper" v-show="ifTitleAndMenuShow" :class="{'hide-box-shadow': ifSettingShow || !ifTitleAndMenuShow}">
        <div class="icon-wrapper" @click="showSetting(3)">
          <i class="iconfont icon-menu"></i>
        </div>
        <div class="icon-wrapper" @click="showSetting(2)">
          <i class="iconfont icon-progress"></i>
        </div>
        <div class="icon-wrapper" @click="showSetting(1)">
          <i class="iconfont icon-sun"></i>
        </div>
        <div class="icon-wrapper">
          <i class="iconfont" @click="showSetting(0)">A</i>
        </div>
      </div>
    </transition>
    <transition name="slide-up">
      <div class="setting-wrapper" v-if="ifSettingShow">
        <div class="setting-font-size" v-if="showTag === 0">
          <div class="preview small-case" :style="{fontSize:fontSizeList[0].fontSize+'px'}">A</div>
          <div class="select">
            <div class="select-wrapper" v-for="(item,index) in fontSizeList" :key="index" @click="setFontSize(item.fontSize)">
              <div class="line"></div>
              <div class="point-wrapper">
                <div class="point" v-show="defaultFontsize === item.fontSize">
                  <!-- 小圆 -->
                  <div class="small-point">
                  </div>
                </div>
              </div>
              <div class="line"></div>
            </div>
          </div>
          <div class="preview big-case" :style="{fontSize:fontSizeList[fontSizeList.length-1].fontSize+'px'}">A</div>
        </div>
        <div class="setting-themes" v-else-if="showTag === 1">
          <div class="setting-themes-item" v-for="(item,index) in themesList" :key="index" @click="setThemes(index)">
            <div :class="['preview', item.style.body.background !== '#fff'?'':'add-border']" :style="{'background':item.style.body.background}"></div>
            <div class="text" :class="{'selected': index === defaultThemes}">{{item.name}}</div>
          </div>
        </div>
        <!-- 书本进度条位置 -->
        <div class="setting-progress" v-else-if="showTag === 2">
          <div class="progress-wrapper">
            <input
            class="progress"
            type="range"
            max="100"
            min="0"
            step="1"
            ref="progress"
            @change="onProgressChange($event.target.value)"
            @input="onProgressInput($event.target.value)"
            :disabled="!bookAvailable"
            :value="progress"/>
          </div>
          <div class="text-wrapper">
            <span>{{bookAvailable ? progress + '%' : '加载中...'}}</span>
          </div>
        </div>
      </div>
    </transition>
    <content-view
      :ifShowContent="ifShowContent"
      v-show="ifShowContent"
      :navigation="navigation"
      :bookAvailable="bookAvailable"
      @jumpTo="jumpTo"
      >
    </content-view>
    <!-- 目录的蒙版 -->
    <transition name="fade">
      <div class="content-mask"
      v-show="ifShowContent"
      @click="hideContent"
      >
      </div>
    </transition>
  </div>
</template>

<script>
import contentView from '@/components/Content.vue'
export default {
  props: {
    ifTitleAndMenuShow: {
      type: Boolean,
      default: false
    },
    fontSizeList: Array,
    defaultFontsize: Number,
    themesList: Array,
    defaultThemes: Number,
    bookAvailable: Boolean,
    navigation: Object
  },
  components: {
    contentView
  },
  data() {
    return {
      ifSettingShow: false,
      showTag: 0,
      progress: 0,
      ifShowContent: false
    }
  },
  methods: {
    jumpTo(target) {
      console.log(123)
      this.$emit('jumpTo', target)
    },
    hideContent() {
      this.ifShowContent = false
    },
    onProgressInput(progress) {
      this.progress = progress
      this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`
    },
    onProgressChange(progress) {
      this.$emit('onProgressChange', progress)
    },
    setThemes(index) {
      this.$emit('setThemes', index)
    },
    showSetting(tag) {
      this.showTag = tag
      this.ifSettingShow = true
      if (tag === 3) {
        // 要让目录显示出来
        this.ifShowContent = true
        this.ifSettingShow = false
      }
      // this.$nextTick(() => {
      //   let selectWrapper = document.querySelector('.select-wrapper')
      //   // 获得每个线得宽度
      //   let lineWidth = (selectWrapper.offsetWidth - 1) / 2
      //   // 设置A得位置
      //   let smallCase = document.querySelector('.small-case')
      //   let bigCase = document.querySelector('.big-case')
      //   // 让文字居中，计算需要给定得padding值
      //   let num = smallCase.offsetWidth - (smallCase.offsetWidth + lineWidth) / 2
      //   smallCase.style.paddingRight = num / 37.5 + 'rem'
      //   bigCase.style.paddingLeft = num / 37.5 + 'rem'
      // })
    },
    hideSetting() {
      this.ifSettingShow = false
    },
    setFontSize(fontSize) {
      this.$emit('setFontSize', fontSize)
    }
  }
}
</script>
<style  scoped lang='scss'>
@import '../assets/styles/global.scss';
.menu-bar {
  .setting-wrapper {
    position: absolute;
    bottom: px2rem(48);
    left:0;
    z-index:9;
    width: 100%;
    height: px2rem(60);
    box-shadow: 0 px2rem(-8) px2rem(8) rgba(0,0,0,.3);
    background: #fff;
    .setting-font-size {
      display: flex;
      height: 100%;
      .preview {
        flex: 0 0 px2rem(40);
        display: flex;
        align-items: center;
        &.small-case {
          justify-content: flex-end;
        }
      }
      .select {
        display: flex;
        flex:1;
        .select-wrapper {
          flex: 1;
          display: flex;
          align-items: center;
          // background-color: yellow;
          &:first-child {
            // background-color: orange;
            .line {
              &:first-child {
                border-top: none;
              }
            }
          }
          &:last-child {
            .line {
              &:last-child {
                border-top: none;
              }
            }
          }
          .line {
            flex: 1;
            height: 0;
            border-top: px2rem(1) solid #ccc;
          }
          .point-wrapper {
            position: relative;
            flex:0 0 0;
            width: 0;
            height:px2rem(7);
            border-left:px2rem(1) solid #ccc;
            .point {
              position: absolute;
              top:px2rem(-8);
              left:px2rem(-10);
              width: px2rem(20);
              height: px2rem(20);
              border-radius: 50%;
              background-color: #fff;
              border:px2rem(1) solid #ccc;
              box-shadow: 0 px2rem(4) px2rem(4) rgba(0, 0, 0, .15);
              @include center;
              .small-point {
                width:px2rem(5);
                height:px2rem(5);
                background-color: #000;
                border-radius: 50%;
              }
            }
          }
        }
      }
    }
    .setting-themes {
      height: 100%;
      display: flex;
      .setting-themes-item {
        flex:1;
        display:flex;
        flex-direction: column;
        box-sizing: border-box;
        padding: px2rem(5);
        .preview {
          flex: 1;
          &.add-border {
            border:px2rem(1) solid #ccc;
            box-sizing: border-box;
          }
        }
        .text {
          flex: 0 0 px2rem(30);
          font-size: px2rem(16);
          color:#ccc;
          @include center;
          &.selected {
            color:#333;
          }
        }

      }
    }
    .setting-progress {
      position: relative;
      width: 100%;
      height:100%;
      .progress-wrapper {
        width: 100%;
        height: 100%;
        @include center;
        padding:0 px2rem(30);
        box-sizing: border-box;
        .progress {
          width: 100%;
          // 去除原生标签的所有样式
          -webkit-appearance: none;
          height: px2rem(2);
          background: linear-gradient(#999,#999) no-repeat,#ddd;
          background-size: 0 100%;
          &:focus {
            outline: none;
          }
          // 原生input标签的滑块
          &::-webkit-slider-thumb {
            -webkit-appearance: none;
            height: px2rem(20);
            width:px2rem(20);
            border-radius: 50%;
            background: #fff;
            box-shadow: 0 4px 4px 0 rgba(0,0,0,.15);
            border:px2rem(1) solid #ddd;
          }
        }
      }
      .text-wrapper {
        position: absolute;
        bottom: 0;
        width: 100%;
        text-align: center;
        font-size: px2rem(16);
      }
    }
  }
  .menu-wrapper {
    position: absolute;
    bottom:0;
    left:0;
    width: 100%;
    height: px2rem(48);
    background: white;
    z-index: 99;
    display: flex;
    box-shadow: 0 px2rem(-5) px2rem(10) rgba(0, 0, 0, .3);
    &.hide-box-shadow {
      box-shadow: none;
    }
    .icon-wrapper {
      flex: 1;
      @include center;
      .icon-progress {
        font-size: px2rem(26);
      }
      .icon-sun {
         font-size: px2rem(22);
      }
    }
  }
  .content-mask {
    position: absolute;
    top:0;
    left:0;
    z-index: 101;
    display: flex;
    width: 100%;
    height: 100%;
    background: rgba(51, 51, 51, .8);
  }
  .fade-enter, .fade-leave-to {
    opacity:0;
  }
  .fade-enter-to, .fade-leave {
    opacity: 1;
  }
  .slide-right-enter, .slide-right-leave-to {
    transform: translate3d(-100%,0,0);
  }
  .slide-right-enter-to, .slide-right-leave {
    transform: translate3d(0,0,0);
  }
}
// 理解动画的含义
/*
 v-enter-from  入场动画最初的一个效果是什么样的
 v-enter-to  入场结束的一个状态是什么样的
 v-enter-active  定义是如何动画或者如何过渡的（在整个动画中如何去执行））
 v-leave-from 出场动画最初的一个状态
 v-enter-to 出场结束的状态是什么样的
 v-leave-active,一般出场和入场的动画过程是一样的
 */
</style>
