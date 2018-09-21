<template>
  <transition name="slide-right">
    <div class="content">
      <div class="content-wrapper" v-if="bookAvailable">
        <div class="content-item" @click="jumpTo(item.href)" v-for="(item,index) in navigation.toc" :key="index">
          <span class="text">{{item.label}}</span>
        </div>
      </div>
      <div class="empty" v-else>加载中...</div>
    </div>
  </transition>
</template>

<script>
export default {
  components: {},
  props:{
    ifShowContent: Boolean,
    navigation: Object,
    bookAvailable: Boolean
  },
  data() {
    return {
    }
  },
  watch: {},
  computed:{},
  methods:{
    jumpTo(target){
      this.$emit('jumpTo',target)
    }
  },
  created(){},
  mounted(){}
}
</script>
<style lang="scss" scoped>
@import '../assets/styles/global';
.content{
  position: absolute;
  left: 0;
  top: 0;
  width: 80%;
  height: 100%;
  overflow-y: scroll;
  display: flex;

  font-size: px2rem(15);
  background: white;
  z-index: 102;
  .content-wrapper {

    flex:1;
     display: flex;
    flex-direction: column;
    overflow: hidden;
    padding: px2rem(10);
    box-sizing: border-box;
    .content-item {
      padding: px2rem(5);
      min-height: px2rem(20);

      overflow: hidden;
      .text{
      }
    }
  }
  .empty{
    flex: 1;
    @include center;
  }
}
</style>
