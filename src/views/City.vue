<template>
  <div class="city" ref="area_scroll">
    <div class="search_wrap">
      <div class="search">
        <i class="fa fa-search"></i>
        <input   v-model="city_val" type="text" placeholder="输入城市名">
      </div>
      <button @click="$router.push({name:'address',params:{city:city}})">取消</button>
    </div>
    <div style="height:100%" v-if="searchList.length==0">
      <div class="location">
         <Location :address="city" @click="selectcity({name:city})"/>
      </div>
      <Alphabet :keys="keys" ref="allcity" :cityInfo="cityInfo"/>
    </div>
    <div class="search_list" v-else>
      <ul>
        <li @click="selectcity(item)" 
        v-for="(item,index) in searchList" :key="index">
          {{item.name}}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import Location from '../components/Location'
import Alphabet from '../components/Alphabet'
export default {
  name: "City",
  data(){
    return {
        city_val: "",
      cityInfo:null,
      keys:[],
       allCities: [],
      searchList:[]
    }
  },
  components:{
    Location,
    Alphabet
  },
  methods:{
    
    selectcity(city){
      // console.log(11)
      this.$router.push({name:'address',params:{
        city:city.name
      }})
    },
    getCityInfo(){
    
      this.$axios("/api/posts/cities").then(res=>{
        this.cityInfo=res.data;
        this.keys=Object.keys(res.data);
        this.keys.pop();
        this.keys.sort();
        this.$nextTick(()=>{
          this.$refs.allcity.initScroll();
        });
        this.keys.forEach(key => {
          this.cityInfo[key].forEach(city =>{
            this.allCities.push(city)
          })
        })
      })
    },
  searchCity(){
    if(!this.city_val){
      this.searchList=[];
    }else{
      this.searchList = this.allCities.filter(item=>{
        return item.name.indexOf(this.city_val) != -1;
      })
      console.log(this.searchList)
    }
  }
  
  },
  watch:{
    city_val(){
      this.searchCity();
    } 
  },
  computed:{
    city(){
      return this.$store.getters.location.addressComponent.city
    }
  },
  created(){
    this.getCityInfo()
  }
};
</script>

<style scoped>
.city {
  width: 100%;
  height: 100%;
  overflow: auto;
  box-sizing: border-box;
  padding-top: 45px;
}
.search_wrap {
  position: fixed;
  top: 0;
  height: 45px;
  width: 100%;
  background: #fff;
  box-sizing: border-box;
  padding: 3px 16px;
  display: flex;
  justify-content: space-between;
}
.search {
  background-color: #eee;
  border-radius: 10px;
  line-height: 40px;
  width: 85%;
  box-sizing: border-box;
  padding: 0 16px;
}
.search input {
  background: #eee;
  outline: none;
  border: none;
  margin-left: 5px;
}
.search_wrap button {
  outline: none;
  color: #009eef;
}

.location {
  background: #fff;
  padding: 8px 16px;
  height: 65px;
  box-sizing: border-box;
}

.search_list {
  background: #fff;
  padding: 5px 16px;
}
.search_list li {
  padding: 10px;
  border-bottom: 1px solid #eee;
}
</style>
