<template>
  <div :class="{'open':isSort || isScreen}" @click.self="hideView">
    <!-- 导航 -->
     
    <div v-if="filterData" :class="{'filter_wrap':true,'fix':choose}">
      <aside class="filter">
        <div
          class="filter-nav"
           :class="{'filter-bold':currentFilter==index}"
          v-for="(item,index) in filterData.navTab"
          :key="index"
          @click="filterSort(index)"
        >
          <span>{{item.name}}</span>
          <i v-if="item.icon" :class="'fa fa-'+item.icon"></i>
        </div>
      </aside>
    </div>
   
    <!-- 排序 -->
    <section class="filter-extend" v-if="isSort">
      <ul>
        <li @click="selectSort(item,index)" v-for="(item,index) in filterData.sortBy" :key="index">
          <span :class="{'selectName':currentSort == index}">{{item.name}}</span>
          <i v-show="currentSort == index" class="fa fa-check"></i>
        </li>
      </ul>
    </section>
    <!-- 筛选 -->
    <section class="filter-extend" v-if="isScreen">
      <div class="filter-sort">
        <div v-for="(screen,index) in filterData.screenBy" :key="index" class="morefilter">
          <p class="title">{{screen.title}}</p>
          <ul>
            <li
              v-for="(item,i) in screen.data"
              :key="i"
              :class="{'selected':item.select}"
              @click="selectScreen(item,screen)"
            >
              <img v-if="item.icon" :src="item.icon" alt>
              <span>{{item.name}}</span>
            </li>
          </ul>
        </div>
      </div>
      <div class="morefilter-btn">
        <span  @click="clearFilter" :class="{'edit':edit}" class="morefilter-clear">清空</span>
        <span  @click="filterOk" class="morefilter-ok">确定</span>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "FilterView",
  data(){
    return {
      choose:false,
      currentFilter: 0,
      isSort: false,
      currentSort: 0,
      isScreen: false
    }
  },
  computed:{
    edit(){
      let edit = false;
      this.filterData.screenBy.forEach((screen)=>{
        screen.data.forEach(item=>{
          if(item.select){
            edit = true;
          }
        })
      })
      return edit
    }
  },
  methods:{
    clearFilter(){
      this.filterData.screenBy.forEach(screen=>{
        screen.data.forEach(item=>{
          item.select=false;
        })
      })
    },
    filterOk(){
      let screenData = {
        MPI:"",
        offer:"",
        per:""
      }
      let mpiStr = "";
      // console.log(this.filterData);
      this.filterData.screenBy.forEach(screen =>{
        screen.data.forEach((item,index)=>{
          if(item.select){
            if(screen.id != "MPI"){
              screenData[screen.id] = item.code;
            }else{
              mpiStr+=item.code + ",";
              screenData[screen.id] = mpiStr;
            }
          }
        })
      })
      this.$emit("update",{condition:screenData});
      this.hideView();
    },
    filterSort(index){
      this.choose=!this.choose;
      this.currentFilter=index;
      switch(index){
        case 0:
          this.isScreen=false;
          this.isSort=true;
           this.$emit("searchFixed", true);
          break;

        case 1:
          this.$emit("update",{
            condition:this.filterData.navTab[1].condition
          })
          this.hideView();
          break;
        case 2:
          this.$emit("update",{condition:this.filterData.navTab[2].condition})
          this.hideView();
          break;
        case 3:
          this.isSort=false;
          this.isScreen=true;
          this.$emit("searchFixed",true);
          break;
      }
    },
    selectScreen(item,screen){
      this.choose=!this.choose;
      if(screen.id != "MPI"){
        screen.data.forEach(ele=>{
          ele.select = false;
        })
      }
      item.select = !item.select; 
    },
    hideView(){
      this.choose=false;
      this.isScreen=false;
      this.isSort=false;
       this.$emit("searchFixed", false);
    },
    selectSort(item,index){
      this.currentSort=index;
      this.filterData.navTab[0].name=this.filterData.sortBy[index].name;
      this.hideView();
      this.$emit("update",{condition:item.code})
    }
  },
  props:{
    filterData:Object
  }
};
</script>

<style scoped>
.filter_wrap {
  background: #fff;
  position: sticky;
  top: 54px;
  z-index: 10;
}
.fix{
  position: fixed;
  width:100%;
}
.filter {
  position: relative;
  border-bottom: 1px solid #ddd;
  line-height: 10.4vw;
  z-index: 101;
  height: 10.666667vw;
  display: flex;
  justify-content: space-around;
}
.filter-nav {
  flex: 1;
  text-align: center;
  color: #666;
  font-size: 0.8333rem;
}
.filter-nav i {
  width: 1.6vw;
  height: 0.8vw;
  margin-left: 1.333333vw;
  margin-bottom: 0.533333vw;
  fill: #333;
  will-change: transform;
}

.filter-bold {
  font-weight: 600;
  color: #333;
}

.open {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  transition: all 0.3s ease-in-out;
  z-index: 3;
}

.filter-extend {
  background-color: #fff;
  color: #333;
  padding-top: 2.133333vw;
  position: absolute;
  width: 100%;
  z-index: 4;
  left: 0;
  top: 24.533333vw;
}
.filter-extend li {
  position: relative;
  padding-left: 5.333333vw;
  line-height: 10.666667vw;
  overflow: hidden;
}
.fa-check {
  float: right;
  color: #009eef;
  margin-right: 3.733333vw;
  line-height: 10.666667vw;
}

.selectName {
  color: #009eef;
}
/* 筛选 */
.filter-sort {
  background: #fff;
  padding: 0 2.666667vw;
  line-height: normal;
}
.morefilter {
  margin: 2.666667vw 0;
  overflow: hidden;
}
.morefilter .title {
  margin-bottom: 2vw;
  color: #666;
  font-size: 0.5rem;
}
.morefilter ul {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  font-size: 0.8rem;
}
.morefilter li {
  box-sizing: border-box;
  width: 30%;
  height: 9.333333vw;
  line-height: 9.333333vw;
  margin: 0.8vw 1%;
  background: #fafafa;
}
.morefilter li img {
  width: 3.466667vw;
  height: 3.466667vw;
  vertical-align: middle;
  margin-right: 0.8vw;
}
.morefilter li span {
  margin-left: 2%;
  vertical-align: middle;
}

.morefilter-btn {
  display: flex;
  justify-content: space-around;
  align-items: center;
  background-color: #fafafa;
  box-shadow: 0 -0.266667vw 0.533333vw 0 #ededed;
  line-height: 11.466667vw;
  box-sizing: border-box;
}
.morefilter-btn span {
  font-size: 0.826667rem;
  text-align: center;
  text-decoration: none;
  flex: 1;
}
.morefilter-clear {
  color: #ddd;
  background: #fff;
}
.morefilter-ok {
  color: #fff;
  background: #00d762;
  border: 0.133333vw solid #00d762;
}

.selected {
  color: #3190e8 !important;
  background-color: #edf5ff !important;
}

.edit {
  color: #333 !important;
}
</style>
