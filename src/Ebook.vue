<template>
  <div class="ebook">
    <title-bar :ifShow="ifShow"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleShow"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar :ifShow="ifShow" :fontSizeList="fontSizeList" :defaultFontSize="defaultFontSize"
    @setFontSize="setFontSize" 
    :themeList="themeList" :defaultTheme="defaultTheme" :bookAvaliable="bookAvaliable" @setTheme="setTheme" @onProgressChange="onProgressChange" @jumpTo="jumpTo" :navigation="navigation" ref="menuBar"></menu-bar>
  </div>

</template>

<script>
  import TitleBar from '@/components/TitleBar';
  import MenuBar from '@/components/MenuBar'
  import Epub from 'epubjs'
  const Download_url = '/static/113368.epub'

  export default {
    components: {
      TitleBar,
      MenuBar
    },
    data() {
      return{
        ifShow: false,
        fontSizeList: [
          {fontSize: 12},
          {fontSize: 14},
          {fontSize: 16},
          {fontSize: 18},
          {fontSize: 20},
          {fontSize: 22},
          {fontSize: 24}
        ],
        defaultFontSize: 16,
        themeList: [
          {
            name: 'default',
            style: {
              body: {
                'color': '#000', 'background': '#fff'
              }
            }
          },
          {
            name: 'eye',
            style: {
              body: {
                'color': '#000', 'background': '#ceeaba'
              }
            }
          },
          {
            name: 'night',
            style: {
              body: {
                'color': '#fff', 'background': '#000'
              }
            }
          },
          {
            name: 'gold',
            style: {
              body: {
                'color': '#000', 'background': 'rgb(241,236,226)'
              }
            }
          }
        ],
        defaultTheme: 0,
        // 图书是否可用
        bookAvaliable: false,
        navigation: {}
      }
    },
    methods: {
      jumpTo(href) {
        this.rendition.display(href)
        this.hideTitleAndMenu()
      },
      hideTitleAndMenu(){
        this.ifShow = false
        this.$refs.menuBar.hideSetting()
        this.$refs.menuBar.hideContent()
      },    
      onProgressChange(progress) {
        const percentage = progress / 100
        const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0
        this.rendition.display(location)
      },
      setTheme(index) {
        this.themes.select(this.themeList[index].name)
        this.defaultTheme = index
      },
      registerTheme(){
        this.themeList.forEach(theme => {
          this.themes.register(theme.name, theme.style)
        })
      },
      setFontSize(fontSize) {
        this.defaultFontSize = fontSize;
        if(this.themes){
          this.themes.fontSize(fontSize + 'px')
        }
      },
      toggleShow() {
        this.ifShow = !this.ifShow;
        if(!this.ifShow) {
          this.$refs.menuBar.hideSetting();
        }
      },
      prevPage() {
        // rendition.prev
        if (this.rendition) {
          this.rendition.prev();
        }
      },
      nextPage() {
        if (this.rendition) {
          this.rendition.next();
        }
      },
      // 电子书的解析和渲染
      showEpub() {
        // 生成Book
        this.book = new Epub(Download_url) 
        // 生成Rendition
        this.rendition = this.book.renderTo('read', {
            width: window.innerWidth,
            height: window.innerHeight
          }
        ) 
        // 通过Rendition.display渲染电子书
        this.rendition.display()
        // 获取Theme对象
        this.themes = this.rendition.themes
        // 设置默认字体
        this.setFontSize(this.defaultFontSize)
        // 设置主题register。select
        this.registerTheme()
        this.setTheme(this.defaultTheme)
        // 获取locations对象
        // 通过epub的钩子函数实现电子书定位
        this.book.ready.then(() => {
          this.navigation = this.book.navigation
          return this.book.locations.generate()
        }).then(result => {
          this.locations = this.book.locations
          this.bookAvaliable = true
        })
      }
    }

    ,
    mounted() {
      this.showEpub()
    }
  }

</script>

<style lang='scss' scoped>
  @import 'assets/styles/global';

  .ebook {
    position: relative;
    overflow: hidden;
    .read-wrapper {
      .mask {
        position: absolute;
        top: 0;
        left: 0;
        z-index: 100;
        display: flex;
        width: 100%;
        height: 100%;

        .left {
          flex: 0 0 px2rem(100);
        }

        .center {
          flex: 1;
        }

        .right {
          flex: 0 0 px2rem(100);
        }
      }
    }


  }

</style>
