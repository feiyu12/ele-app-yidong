<template>
  <div :class="{header:true,'header-sea':flag}">
    <!-- 左侧返回 -->
    <div class="header-button is-left" v-show="isLeft">
      <i class="fa fa-chevron-left"></i>
      <button @click="$router.go(-1)">返回</button>
    </div>
    <!-- 中间标题 -->
    <h1 class="header-title">{{title}}</h1>
  </div>
</template>

<script>
export default {
  name: "Header",
  data(){
    return {
      flag:false
    }
  },
  props: {
    title: String,
    seaH:String,
    isLeft: {
      type: Boolean,
      default: false
    }
  },
  mounted(){
    if(this.seaH){
      switch(this.seaH){
        case '54px':
          this.flag=true;
          break;
      }
    }
  }
};
</script>

<style scoped>
.header {
  align-items: center;
  background-color: #009eef;
  box-sizing: border-box;
  color: #fff;
  display: flex;
  font-size: 16px;
  height: 45px;
  line-height: 1;
  padding: 0 10px;
  position: relative;
  text-align: center;
  white-space: nowrap;
  position: fixed;
  width: 100%;
  top: 0;
  z-index: 99;
}
.header-sea{
  height: 54px;
}

.header-button button {
  background-color: transparent;
  border: 0;
  box-shadow: none;
  color: inherit;
  display: inline-block;
  padding: 0;
  font-size: inherit;
  outline: none;
}
.header-title {
  flex: 1;
}
.is-left {
  text-align: left;
}
</style>
