<template>
  <div class="ebook">
    <title-bar :ifTitleAndMenuShow="ifTitleAndMenuShow"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleTitleAndMenu"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar :navigation="navigation" @jumpTo="jumpTo" @onProgressChange="onProgressChange" :bookAvailable="bookAvailable" @setTheme="setTheme" :defaultTheme="defaultTheme" :themeList="themeList" @setFontSize="setFontSize" :defaultFontSize="defaultFontSize" :fontSizeList="fontSizeList" :ifTitleAndMenuShow="ifTitleAndMenuShow" ref="menuBar"></menu-bar>
  </div>
</template>

<script>
import TitleBar from '@/components/TitleBar'
import MenuBar from '@/components/MenuBar'
import Epub from 'epubjs'
global.epub = Epub
const DOWNLOAD_URL = '/static/book/tomorrow.epub'
export default {
  components: {
    TitleBar,
    MenuBar
  },
  props: {},
  data () {
    return {
      ifTitleAndMenuShow: false,
      fontSizeList: [
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 },
      ],
      defaultFontSize: 18,
      themeList: [
        {
          name: 'default',
          style: {
            body: {
              'color': '#000','background': '#fff'
            }
          }
        },
        {
          name: 'eys',
          style: {
            body: {
              'color': '#000','background': '#ceeaba'
            }
          }
        },
        {
          name: 'night',
          style: {
            body: {
              'color': '#fff','background': '#000'
            }
          }
        },
        {
          name: 'gold',
          style: {
            body: {
              'color': '#000','background': 'rgb(241,234,226)'
            }
          }
        },
      ],
      defaultTheme: 0,
      // 图书是否处于可用状态
      bookAvailable: false

    }
  },
  created () {},
  mounted () {
    this.showEpub()
  },

  methods: {
    //  根据链接跳转到指定位置
    jumpTo(href) {
      this.rendition.display(href)
      this.hideTitleAndMenu()
    },
    hideTitleAndMenu() {
      //  隐藏标题栏和菜单栏
      this.ifTitleAndMenuShow =  false
      //  隐藏菜单栏弹出的设置栏
      this.$refs.menuBar.hideSetting()
      //  隐藏目录
      this.$refs.menuBar.hideContent()
    },
    //  progress 进度条的数值 (0-100)
    onProgressChange(progress) {
      const percentage = progress / 100;
      const location =percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
      this.rendition.display(location)
    },

    setTheme(index) {
      if(this.themes){
        this.themes.select(this.themeList[index].name);
      }

      this.defaultTheme=index;
    },
    //注册主题
    registerTheme() {
      this.themeList.forEach(theme => {
        this.themes.register(theme.name, theme.style)
      })
    },
    //设置字体大小
    setFontSize(fontSize){
      if(this.themes){
        this.themes.fontSize(fontSize+'px')
        this.defaultFontSize=fontSize;
      }

    },
    // 标题栏和菜单的显示
    toggleTitleAndMenu() {
      this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow;
      if(!this.isTitleAndMenuShow){
        this.$refs.menuBar.hideSetting();
      }
    },
    // 上一页
    prevPage() {
      // Rendition.prev
      if (this.rendition) {
        this.rendition.prev()
      }
    },
    // 下一页
    nextPage() {
      // Rendition.next
      if (this.rendition) {
        this.rendition.next()
      }
    },
    // 电子书的解析和渲染
    showEpub() {
      //  生成Book
      this.book = new Epub(DOWNLOAD_URL)
      console.log(this.book)
      //  生成Rendition
      this.rendition = this.book.renderTo('read', {
        'width': window.innerWidth,
        'height': window.innerHeight
      })
      //  通过Rendtion.display渲染电子书
      this.rendition.display();

      //  获取主题
      this.themes = this.rendition.themes;

      //
      this.registerTheme();
      // this.themes.select('night');

      //  获取locations对象
      this.book.ready.then(() => {
        this.navigation = this.book.navigation
        return this.book.locations.generate();
      }).then(() => {
        this.locations=this.book.locations;
        this.bookAvailable=true;
      })
    }
  }
}
</script>
<style lang="scss" scoped>
@import 'assets/styles/global';
.ebook{
  position: relative;

  .read-wrapper{
    .mask{
      position: absolute;
      top: 0;
      left: 0;
      z-index:100;
      display: flex;
      width: 100%;
      height: 100%;

      .left{
        flex:0 0 px2rem(100);
      }
      .center{
        flex:1;
      }
      .right{
        flex:0 0 px2rem(100);
      }

    }
  }

}
</style>
