<template>
 <transition name="slide-right">
   <div class="content">
     <!-- 书本加载完毕之后才可以有目录 -->
     <div class="content-wrapper" v-if="bookAvailable" key="box1">
      <div class="content-item" v-for="(item,index) in navigation.toc" :key="index" @click="jumpTo(item.href)">
        <span class="text">{{item.label}}</span>
      </div>
     </div>
     <div v-else class="empty" key="box2">加载中...</div>
   </div>
 </transition>
</template>

<script>
export default {
  props: {
    bookAvailable: Boolean,
    navigation: Object
  },
  methods: {
    jumpTo(target) {
      console.log(123)
      this.$emit('jumpTo', target)
    }
  }
}
</script>
<style  scoped lang='scss'>
@import '../assets/styles/global.scss';
 .content {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 102;
    width: 80%;
    height: 100%;
    background: white;
    .content-wrapper {
      width: 100%;
      height: 100%;
      overflow: auto;
      .content-item {
        padding: px2rem(20) px2rem(15);
        border-bottom: px2rem(1) solid #f4f4f4;
        .text {
          font-size: px2rem(14);
          color: #333;
        }
      }
    }
    .empty {
      width: 100%;
      height: 100%;
      @include center;
      font-size: px2rem(16);
      color: #333;
    }
  }
</style>
