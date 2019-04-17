<template>

          <div id="login" class="LoginContent">
           <!-- <yd-search v-model="value1" :on-submit="submitHandler" placeholder="地址"></yd-search> -->
            <!-- <el-button :plain="true" @click="open">错误</el-button> -->
            <img :src="ImgUrl"  alt="" class='ImgClass'>
            <el-form ref="form" :model="form"  @keyup.enter.native="onSubmit(form)" class="form">
              <el-form-item>
                <span class="InputImg"></span>
                <el-input  type="text" placeholder="请输入用户名" v-model="form.user"></el-input>
              </el-form-item>

              <el-form-item>
                <span class="InputImgPass"></span>
                <el-input type="password" placeholder="请输入密码" v-model="form.password"></el-input>
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="onSubmit(form)" style="width:100%;">登录</el-button>
                            <!-- <el-button @click="diao()" type="primary">调用</el-button> -->
              </el-form-item>
              <el-form-item>
                <el-button type="primary" @click="openUrl" style="width:100%;">设置API地址</el-button>
                  
              </el-form-item>
            </el-form>
          </div>
</template>   

<script scoped>
export default {
  name: 'login',
    beforeRouteEnter (to, from, next) {
        next(vm=>{
          let data=vm.getCookie("userCode");
          if(data){
            next({path:"/home"})
          }else{
            next();
          }
        })
  },
  data () {
    return {
      form:{
        user:'',   
        password:'',
      },
      ImgUrl:'',
      // value1:resUrl,
    }
  },
  mounted(){
        this.ImgUrl=require("../assets/logo.png");    
        var that=this;
        document.addEventListener("plusready", function () {  
            let data = plus.storage.getItem("url");
              if(data!=null){
                  window.resUrl=data;
                  // that.value1=data;
                }
        }, false);     
  },
  methods:{
    openUrl(){
      this.$prompt('请输入地址', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          beforeClose: (action, instance, done) => {
           
            if (action === 'confirm') {
              if(window.plus){
                  void plus.storage.setItem("url", instance.inputValue);
              }else{
                  document.addEventListener('plusready',function(){
                  void plus.storage.setItem("url", instance.inputValue);
                  },false);
              }
                window.resUrl=instance.inputValue;
                done();
            } else {
              done();
            }
          }
        }).then(({ value }) => {
          this.$message({
            type: 'success',
            message: '你的地址是: ' + value
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '取消输入'
          });       
        });
    },
    // submitHandler(val){
    // if(window.plus){
    //     void plus.storage.setItem("url", val);
    // }else{
    //     document.addEventListener('plusready',function(){
    //     void plus.storage.setItem("url", val);
    //     },false);
    // }
    //   window.resUrl=val;
    // },
    onSubmit(data){
      let formdata=this.form;
      if(formdata.user==""||formdata.password==""){
         this.open();
      }else{
        let datee={UserName:formdata.user,PassWord:formdata.password}
        this.$http.post('/API/loginservice/Login',{UserName:formdata.user,PassWord:formdata.password},{emulateJSON: true}).then(res=>{ 
          if(res.data.resultNum=="1"){
          this.pageData = res.data.subjects;
          let data = res.data.baseMeno1;
          this.setCookie("userCode",data);
          window.localStorage.setItem('token', data);
          this.$router.push({path:'/home'});

          //  global.baseMeno1=JSON.parse(res.data.baseMeno1);
          }else{
            this.open();
          }
        })
      }
    },
     open:function() {
       this.$message({
          showClose: true,
          message: '用户名密码错误，请重新登录'
        });
      },
  }
}
</script>
<style>
.LoginContent .el-input__inner {
    font-size: 16px;
    padding-left:45px;
    border: 1px solid #e9e9e9;/*no*/
    position: relative;
    background-color:#F2F2F3;
}
.LoginContent{
	width: 100%;
  text-align: center;
  position: absolute;
}
.form{
  margin:0 20%;
}
.ImgClass{
    width: 70%;
    margin-bottom:8%;
    margin-top:28%;
}
.InputImg{
  background:url("../assets/user.png") no-repeat;
  width:15px;
  height:20px;
  background-size:cover;
  position: absolute;
  z-index: 2000;
  top:10px;
  left:12px
}
.InputImgPass{
  background:url("../assets/password.png") no-repeat;
  width:15px;
  height:20px;
  background-size:cover;
  position: absolute;
  z-index: 2000;
  top:10px;
  left:12px
}
.el-input__inner{
  border:1px solid #ccc;/*no*/
}
</style>
