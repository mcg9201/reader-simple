<!--  -->
<template>
  <transition name="slide-right">
    <div class="content">
      <div class="content-wrapper" v-if="bookAvaliable">
        <div class="content-item" v-for="(item, index) in navigation.toc" :key="index" @click="jumpTo(item.href)">
          <span class="text">{{item.label}}</span>
        </div>
      </div>
      <div class="empty" v-else>加载中...</div>
    </div>
  </transition>
</template>

<script>
export default {
  props: {
    ifShowContent: Boolean,
    navigation: Object,
    bookAvaliable: Boolean
  },
  methods: {
    jumpTo(target){
      this.$emit('jumpTo', target)
    }
  }
}
</script>

<style lang='scss' scoped>
  @import "../assets/styles/global";
  .content {
    position: absolute;
    top: 0;
    left: 0;
    background-color: #fff;
    width: 50%;
    height: 100%;
    z-index: 102;
    font-size: px2rem(14);
    overflow-x: hidden;
    overflow-y: auto;
    .content-wrapper{
      .content-item{
        border-bottom: px2rem(1) solid #ccc;
        .text{
          line-height: px2rem(30)
        }
      }
    }
    .empty{
      display: flex;
      @include center;
    }
  }
</style>