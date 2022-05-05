<template>
<div class='ebook'>
  <title-bar :ifTitleAndMenuShow="ifTitleAndMenuShow"></title-bar>
  <div class="reader-wrapper">
    <div id="read"></div>
    <div class="mask">
      <div class="left" @click="prevPage"></div>
      <div class="center" @click="toggleTitleMenu"></div>
      <div class="right" @click="nextPage"></div>
    </div>
  </div>
  <menu-bar
    ref="menubar"
    :themesList="themesList"
    :defaultThemes="defaultThemes"
    @setFontSize="setFontSize"
    @setThemes="setThemes"
    @onProgressChange="onProgressChange"
    @jumpTo="jumpTo"
    :ifTitleAndMenuShow="ifTitleAndMenuShow"
    :fontSizeList="fontSizeList"
    :navigation="navigation"
    :defaultFontsize="defaultFontsize"
    :bookAvailable="bookAvailable"
  >
  </menu-bar>
</div>
</template>

<script>
import Epub from 'epubjs'
import TitleBar from '@/components/TitleBar'
import MenuBar from '@/components/MenuBar'
const DOWNLOAD_URL = '/static/2018_Book_AgileProcessesInSoftwareEngine.epub'
global.ePub = Epub
// 在注释的下方不再检查eslint语法
/* eslint-disable space-before-function-paren */
export default {
  data () {
    return {
      book: {},
      rendition: {},
      ifTitleAndMenuShow: false,
      fontSizeList: [
        {fontSize: 12},
        {fontSize: 14},
        {fontSize: 16},
        {fontSize: 18},
        {fontSize: 20},
        {fontSize: 22},
        {fontSize: 24}
      ],
      themesList: [
        { name: 'default',
          style: {
            body: {
              'color': '#000',
              'background': '#fff'
            }
          }
        },
        { name: 'eye',
          style: {
            body: {
              'color': '#000',
              'background': '#ceeaba'
            }
          }
        },
        { name: 'night',
          style: {
            body: {
              'color': '#fff',
              'background': '#000'
            }
          }
        },
        { name: 'gold',
          style: {
            body: {
              'color': '#000',
              'background': 'rgb(241, 236, 226)'
            }
          }
        }
      ],
      defaultFontsize: 16,
      defaultThemes: 0,
      themes: {},
      locations: {},
      // 书本目录
      navigation: {},
      // 进度条是否可用
      bookAvailable: false
    }
  },
  components: {
    TitleBar,
    MenuBar
  },
  mounted () {
    this.showEpub()
  },
  provide: {
    navigation: () => {
      return this.navigation
    }
  },
  methods: {
    jumpTo(target) {
      // 点击目录跳转
      this.rendition.display(target)
      console.log(target, '目录树')
      // 当目录跳转的时候，隐藏目录栏和工具栏
      this.hideTitleAndMenu()
    },
    hideTitleAndMenu() {
      this.ifTitleAndMenuShow = false
      this.$refs.menubar.hideSetting()
      this.$refs.menubar.hideContent()
    },
    // 进度条数值（0-100
    onProgressChange(progress) {
      const percentage = progress / 100
      const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
      // 渲染书本页数位置
      this.rendition.display(location)
    },
    setThemes(index) {
      this.themes.select(this.themesList[index].name)
      this.defaultThemes = index
    },
    registerTheme() {
      this.themesList.forEach(themes => {
        this.themes.register(themes.name, themes.style)
      })
    },
    setFontSize(fontSize) {
      this.defaultFontsize = fontSize
      if (this.themes) {
        this.themes.fontSize(fontSize + 'px')
      }
    },
    prevPage() {
      if (this.rendition) {
        this.rendition.prev()
      }
    },
    nextPage() {
      if (this.rendition) {
        this.rendition.next()
      }
    },
    toggleTitleMenu() {
      this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow
      if (!this.ifTitleAndMenuShow) {
        this.$refs.menubar.hideSetting()
      }
    },
    // 电子数的解析和渲染
    showEpub () {
      // 生成book
      this.book = new Epub(DOWNLOAD_URL)
      console.log(this.book, '电子对象')
      // 生成Rendition(参数1是dom结构)
      this.rendition = this.book.renderTo('read', {
        width: window.innerWidth,
        Height: window.innerHeight
      })

      // 通过Rendition.display渲染电子数
      this.rendition.display()
      this.themes = this.rendition.themes
      this.themes.fontSize(this.defaultFontsize + 'px')
      // this.themes.register(name,styles)  将主题注册到themes对象中
      // this.themes.select(name)  传递主题名称切换主题
      // 注册电子书主题
      this.registerTheme()
      this.setThemes(this.defaultThemes)
      // 书本得进度控制
      this.locations = this.book.locations
      console.log(this.locations)
      // 通过epubjs得钩子函数类实现
      this.book.ready.then(() => {
        // 书本渲染完成之后可以获得书本的目录信息
        this.navigation = this.book.navigation
        console.log(this.navigation, '目录文件')
        return this.book.locations.generate()
      }).then(result => {
        this.bookAvailable = true
        // 书本渲染完毕之后，本地变量存储
        this.locations = this.book.locations
        this.onProgressChange(0)
      })
    }
  }
}
</script>
<style  scoped lang='scss'>
@import './assets/styles/global.scss';
.ebook {
  position: relative;
  height: 100%;
  .reader-wrapper {
    .mask {
      position: absolute;
      left:0;
      top:0;
      right: 0;
      display: flex;
      bottom: 0;
      // background-color: aqua;
      .left {
        flex:0 0 px2rem(100);
        // background: pink;
      }
      .center {
        flex:1;
        // background: purple;
      }
      .right {
        flex:0 0 px2rem(100);
        // background: yellowgreen;
      }
    }
  }
}
</style>
