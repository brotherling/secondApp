<template>
  <div class="div1">
      <div class="home_top">
        <div @click="click_Inentory">待盘点任务</br><span>{{inventory}}</span>个</div>
        <i></i>
        <div @click="click_Check">待核查任务</br><span >{{check}}</span>个</div>
      </div>
      <section class="HomeContent"> 
       <div class="ShowTop">
        <div class="topdata" v-if="this.toggle">
          <div class="top"><span class="squre"></span>资源状况</div>
          <div class="InMiddle"><span class="setSize">{{link.resultObj2.Value}}</span>总资产</div>
          <div class="datafoot">
            <div class="setflex"><span style="color:#026DB8">{{link.resultObj4.Value}}</span>使用中</div>
            <div class="setflex"><span style="color:#026DB8">{{link.resultObj3.Value}}</span>空闲资产</div>
            <div class="setflex"><span style="color:#026DB8">{{link.resultObj5.Value}}</span>本月新增</div>
          </div>
        </div>
      </div>
          <ul>
            <li v-for="(intext,index) in menu" :key="intext.id" @click="clickmenu1(intext.name,intext.levelCode)">
              <img :src="intext.img" alt="" class="MenuImg">
              <span href="" >{{intext.name}}</span></li>
            <li @click="clickmenu($event)">
              <img src="../assets/grzx.png"  class="MenuImg" alt="">
              <span href="" >个人中心</span></li>
          </ul>
      </section>
      <v-foot ref="homeactive"></v-foot>
  </div>
</template>

<script>
// import {mapGetters} from 'vuex'
export default {
  name: 'home',
  data () {
    return {
        pageData:null,
        showSearch:true,
        link:{resultObj2:{Value:0},resultObj3:{Value:0},resultObj4:{Value:0},resultObj5:{Value:0}},
        toggle:false,
        inventory:"0",
        check:"0",
        menu:[]
    }
  },
  methods:{
    click_Inentory(){
      let name="盘点批次",url="reviewbatch";
      this.$router.push({path:'/reviewbatch/',query: { name:name, id:"104",url:url}});
    },
    click_Check(){
      let name="个人核查",url="grcheck";
      this.$router.push({path:'/grcheck/',query: { name:name, id:"417",url:url}});
    },
    clickmenu(e){
      this.$router.push({path:'/personalCenter/'});
    },
    clickmenu1(text,code){
      console.log(code,text)
      this.$router.push({path:'/homemenu/',query:{id:code,text:text}})
    }
  },
  created(){
     
      global.saodata="";
      var getMenu = window.localStorage.getItem('localMenu') || '[]';
      if(getMenu !== '[]'){
          this.menu = JSON.parse(getMenu)
      }else{
        this.$http.get('/API/Menuservice/GetMenuOne',{emulateJSON: true}).then(res=>{ 
            this.menu=res.data
            window.localStorage.setItem('localMenu', JSON.stringify(res.data));
        });
      }
        this.$http.get('/API/loginservice/GetTJ',{params:{'menuId':"123"}},{emulateJSON: true}).then(res=>{ 
            this.link=JSON.parse(res.data);
            this.link.resultObj2.Value==""?this.link.resultObj2.Value=0:"";
            this.link.resultObj3.Value==""?this.link.resultObj3.Value=0:"";
            this.link.resultObj4.Value==""?this.link.resultObj4.Value=0:"";
            this.link.resultObj5.Value==""?this.link.resultObj5.Value=0:"";
            this.toggle=true;
        })
        this.$http.get('/API/stockservice/GetPCCount',{params:{'menuId':"104"}},{emulateJSON: true}).then(res=>{ 
          if(res.data!=""){
            if(res.data!=""){
            this.inventory=res.data
            }
          }
        })
        this.$http.get('/API/stockservice/GetGrCheckCount',{params:{'menuId':"417"}},{emulateJSON: true}).then(res=>{ 
          if(res.data!=""){
          this.check=res.data;
          }
        })
       
  },
  mounted() {
    //进入对应foot小标高亮
      this.$refs.homeactive.home()
  },
 
}
</script>
<style>
.home_top{
  height:13%;
  width:100%;
  display:flex;
  background:#4186E1;
   align-items: center;
}
.home_top div{
  flex:1;
  color:white;
  font-size:14px;/*no*/
  text-align:center;
  flex-direction: column;
  justify-content: center;
}
.home_top i{
  width:1px;/*no*/
  height:45px;
  background:white;
}
.home_top div span{
  color:white;
  font-size:20px;/*no*/
  display: inline-block;
  margin-top:3px;
}
.div1{
   height:100%;
  }
  .setflex{
    display:flex;
     flex-direction: column;
    align-items: center;
    justify-content: center;
    font-size:18px;
  }
  .ShowTop{
    height:180px;
    width:100%;
    background:#4186E1;
    color:#717071;
  }
  .topdata{
    width:90%;
    height:240px;
    margin-left:5%;
    border:1px solid #eee;/*no*/
    background:white;
    border-radius: 8px;
  }
  .top{
    height: 45px;
    width:100%;
    line-height: 45px;
    border-bottom:1px solid #ccc;/*no*/
    font-size: 18px;
  }
  .InMiddle{
    width:100%;
    height:120px;
    display:flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    font-size: 18px;
  }
  .squre{
    width:20px;
    height: 20px;
    background:#EA5513;
    display:inline-block;
    vertical-align: middle;
    margin:0 10px;
  }
  .setSize{
    font-size:22px;
    color:#EA5513;
    margin-bottom:5px;
  }
  .datafoot{
    height: 65px;
    width:100%;
    display: flex;
    justify-content: space-around;
    align-items: center;
  }
.HomeContent{
  width:100%;
  height:80%;
  /*background:#FAFAFB;*/
    /* padding-top:20px; */
  margin-bottom:10%;
  overflow-y: auto;
  margin-top:-5px;
}
.HomeContent ul{
    margin:0 auto;
  margin-top:70px;
}
.HomeContent ul li{
  width:30%;
  height:120px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  float: left;
  margin-left:10px;
}
.MenuImg{
  width:50px;
  height:50px;
  margin-bottom:5px;
}

</style>
