<template>
  <div class="login">
    <div class="logo">
      <img src="../assets/logo.jpg" alt="my login image">
    </div>
    <!-- 手机号 -->
    <InputGroup
    type="number"
    v-model="phone"
    placeholder="手机号"
    :btnTitle="btnTitle"
    :disabled="disabled"
    :error="errors.phone"
    @btnClick="getVerifyCode"
    ></InputGroup>
    <!-- 验证码 -->
    <InputGroup
    type="number"
    v-model="verifyCode"
    placeholder='验证码'
    :error="errors.code"
    ></InputGroup>
    <!-- 用户服务协议 -->
    <div class="login_des">
      <p>
        新用户登录即自动注册，表示已同意
        <span>《用户服务协议》</span>
      </p>
    </div>
    <!-- 登录按钮 -->
    <div class="login_btn">
      <button  :disabled="isClick" @click="handleLogin">登录</button>
    </div>
  </div>
</template>

<script>
import InputGroup from '../components/InputGroup'
export default {
  name: "login",
  data(){
    return {
      btnTitle:'获取验证码',
      disabled:false,
      errors:{},
      phone:"",
      verifyCode:''
    }
  },
  computed:{
    isClick(){
      if(!this.phone || !this.verifyCode){
        return true;
      }else{
        return false;
      }
    }
  },
  methods:{
    validateBtn(){
      let time = 60;
      let timer = setInterval(()=>{
       if(time == 0){
         clearInterval(timer);
         this.btnTitle="获取验证码";
         this.disabled='false';
       }else{
         this.btnTitle=time + "秒后重试";
         this.disabled=true;
         time=time-1;
       }

      },1000)
    },
    handleLogin(){
      this.errors = {},
      this.$axios.post("/api/posts/sms_back",{
        phone:this.phone,
        code:this.verifyCode
      }).then((res)=>{
        // console.log(res);
        localStorage.setItem('ele_login',res.data.user._id);
        this.$router.push('/');
      })
      .catch(err=>{
        this.errors={
          code:err.response.data.msg
        }
      })

    },
    getVerifyCode(){
      if(this.validatePhone()){
        this.validateBtn();
        this.$axios.post('/api/posts/sms_send',{
          tpl_id:'159118',
          key:"7c861dd76f12a12159c76b48bee551cb",
          phone:this.phone
        }).then(res=>{
          console.log(res)
        })
        
      }
    },
    validatePhone(){
      if(!this.phone){
        this.errors={
          phone:'手机号不能为空'
        }
      }else if(!/^1[345689]\d{9}$/.test(this.phone)){
          this.errors={
            phone:'请填写正确的手机号码'
          }
          return;
      }else{
        this.errors={};
        return true;
      }
    }
  },
  components:{
    InputGroup
  }
};
</script>

<style scoped>
.login {
  width: 100%;
  height: 100%;
  padding: 30px;
  box-sizing: border-box;
  background: #fff;
}

.logo {
  text-align: center;
}
.logo img {
  width: 150px;
}
.text_group,
.login_des,
.login_btn {
  margin-top: 20px;
}
.login_des {
  color: #aaa;
  line-height: 22px;
}
.login_des span {
  color: #4d90fe;
}
.login_btn button {
  width: 100%;
  height: 40px;
  background-color: #48ca38;
  border-radius: 4px;
  color: white;
  font-size: 14px;
  border: none;
  outline: none;
}
.login_btn button[disabled] {
  background-color: #8bda81;
}
</style>
