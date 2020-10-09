<template>
  <div class="Rating-gray">
    <i v-for="(item,index) in itemClasses" :key="index" class="fa" :class="item"></i>
  </div>
</template>

<script>
// 星星长度
const LENGTH = 5;
// 星星对应的class
const CLS_ON = "fa-star";
const CLS_HALF = "fa-star-half-empty";
const CLS_OFF = "fa-star-o";

export default {
  name: "Rating",
  data(){
    return {
      // rating:2.5
    }
  },
  props: {
    rating: Number
  },
  computed: {
   itemClasses(){
     let result = [];
     let integer = Math.floor(this.rating);
     let hasDecimal = this.rating - integer;
     for(let i=0;i<integer;i++){
       result.push(CLS_ON)
     }
     if(hasDecimal>=0.5){
       result.push(CLS_HALF)
     }
     if(this.rating<4.5){
       let norating = 5-Math.round(this.rating);
       for(let i=0;i<norating;i++){
         result.push(CLS_OFF)
       }
     }
     return result
   } 
  }
};
</script>

<style scoped>
.Rating-gray {
  margin-right: 1.066667vw;
  color: #ffbe00;
  display: inline-block;
}
</style>
