<template>
  <div class="search">
    <Header :isLeft="true" title="搜索" :seaH="seaH" />
    <div class="search_header">
      <form class="search_wrap">
        <i class="fa fa-search"></i>
        <input type="text" v-model="key_word" placeholder="输入商家,商品信息" />
        <button @click.prevent="searchHandle">搜索</button>
      </form>
    </div>
    <div class="shop" v-if="result && !showShop">
      <div class="empty_wrap" v-if="empty">
        <img src="https://fuss10.elemecdn.com/d/60/70008646170d1f654e926a2aaa3afpng.png" alt />
        <div class="empty_txt">
          <h4>附近没有搜索结果</h4>
          <span>换个关键词试试吧</span>
        </div>
      </div>
      <div v-else>
        <SearchIndex @click="shopItemClick" :data="result.restaurants"></SearchIndex>
        <SearchIndex @click="shopItemClick" :data="result.words"></SearchIndex>
      </div>
    </div>
    <div class="container" v-if="showShop">
      <!-- 导航 -->
      <FilterView @update="update" :filterData="filterData"></FilterView>
      <div class="shoplist" 
      :infinite-scroll-disabled="loading"
      v-infinite-scroll="loadMore">
        <IndexShop v-for="(item,index) in restaurants" :key="index" :restaurant="item.restaurant"/>
      </div>
    </div>
  </div> 
</template>

<script>
import IndexShop from "../components/IndexShop";
import FilterView from "../components/FilterView";
import Header from "../components/Header";
import SearchIndex from "../components/SearchIndex";
import { InfiniteScroll } from "mint-ui";
export default {
  components: {
    Header,
    SearchIndex,
    FilterView,
    IndexShop
  },
  data() {
    return {
      key_word: "",
      result: [],
      showShop: false,
      empty: false,
      page:0,
      seaH:'54px',
      filterData:null,
      loading:false,
      data:null,
      size:7,
      restaurants:[]
    };
  },
  watch: {
    key_word() {
      this.keyWordChange();
    },
  },
  created(){
    this.$axios("/api/profile/filter").then(res=>{
      this.filterData=res.data;
    })
  },
  methods: {
    loadMore(){
      this.page++;
      this.$axios.post(`/api/profile/restaurants/${this.page}/${this.size}`,this.data)
      .then(res=>{
        if(res.data.length > 0){
          res.data.forEach(item => {
            this.restaurants.push(item);
          });
        }else{
          this.loading = true;
        }
      })
    },
    update(condition){
      // console.log(condition);
      this.data = condition;
      this.shopItemClick();
    },
    shopItemClick(){
      this.page=0;
      this.restaurants=[];
      this.showShop=true;
    },
    keyWordChange() {
      if(!this.key_word){
        return;
      }
      this.$axios(`/api/profile/typeahead/${this.key_word}`)
        .then((res) => {
          this.result = res.data; 
        })
        .catch(() => {
          this.result = null;
        });
    },
    searchHandle() {}
  },
};
</script>

<style scoped>
.search {
  width: 100%;
  height: 100%;
  overflow: auto;
  box-sizing: border-box;
}
.search_header {
  margin-top: 54px;
  background: #fff;
  padding: 0 4.266667vw;
}
.search_header .search_wrap {
  padding: 2.933333vw 2.933333vw 2.933333vw 0;
  display: flex;
  align-items: center;
  position: relative;
}
.search_wrap .fa-search {
  width: 2.933333vw;
  height: 2.933333vw;
  color: #888;
  position: absolute;
  top: 4.6666666vw;
  left: 2.933333vw;
}
.search_wrap input {
  flex-grow: 1;
  border-radius: 0.266667vw;
  background-color: #f8f8f8;
  padding: 1.733333vw 4vw 1.733333vw 8.8vw;
  color: #666;
  outline: none;
  border: none;
}
.search_wrap button {
  outline: none;
  border: none;
  color: #333;
  font-size: 0.426667rem;
  background: #fff;
  font-weight: 700;
  margin-left: 2.933333vw;
  font-size: 14px;
}

.shop {
  width: 100%;
  height: calc(100% - 95px);
  overflow: auto;
}

.empty_wrap {
  width: 100%;
  height: 100%;
  overflow: hidden;
  background: #fff;
  display: flex;
  justify-content: center;
}
.empty_wrap img {
  width: 35vw;
  height: 35vw;
}
.empty_txt h4 {
  color: #666;
  font-size: 1rem;
  margin: 12vw 0 2vw 0;
}
.empty_txt span {
  color: #999;
  font-size: 0.8rem;
}
</style>
