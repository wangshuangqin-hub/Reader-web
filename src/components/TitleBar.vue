<template>
  <transition name="slide-down">
    <div class="title-wrapper" v-show="ifTitleAndMenuShow">
      <div class="left">
        <i class="iconfont icon-back"></i>
      </div>
      <div class="right">
        <div class="icon-wrapper">
          <i class="iconfont icon-shop_car"></i>
        </div>
        <div class="icon-wrapper">
          <i class="iconfont icon-adduser"></i>
        </div>
        <div class="icon-wrapper">
          <i class="iconfont icon-more_android_light"></i>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  props: {
    ifTitleAndMenuShow: {
      type: Boolean,
      default: false
    }
  }
}
</script>
<style  scoped lang='scss'>
@import '../assets/styles/global.scss';
  .title-wrapper {
    position: absolute;
    top:0;
    left:0;
    width: 100%;
    height: px2rem(48);
    background: white;
    z-index: 9;
    display: flex;
    box-shadow: 0 px2rem(5) px2rem(10) rgba(0, 0, 0, .3);
    .left {
      flex:0 0 px2rem(60);
      @include center;
    }
    .right {
      flex: 1;
      display: flex;
      justify-content: flex-end;
      .icon-wrapper {
        flex: 0 0 px2rem(40);
        @include center;
      }
    }
  }
</style>
