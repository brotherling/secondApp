<template>
    <div class="div1">
      <v-header :header="parentMsg"></v-header>
      <section class="HomemenuContent"> 
          <ul>
            <li  v-for="(intext1,index) in Meth" :key="intext1.id" >
              <div v-if="intext1.TreeNodeChild!=null">
                <span class="Jugespan">
                   {{intext1.name}}
                </span>
                <ul class="LevelMenu" id="menu">
                <li v-for="intext2 in intext1.TreeNodeChild" @click="clickmeun3(intext2.name,intext2.id,intext2.purl)" :key="intext2.id">
                <img :src="intext2.img" class="MenuImg" alt="">
                <span>{{intext2.name}}</span>
                </li>
                </ul>
              </div>

              <div v-else class="Second">
               <li class="sanji" @click="clickmeun3(intext1.name,intext1.id,intext1.purl)">
                <img  class="MenuImg" :src="intext1.img" alt="">
                <span>{{intext1.name}}</span>
              </li>
              </div>
            </li>
          </ul>
      </section>
      <v-foot ref="echartactive"></v-foot>
    </div>
</template>
<script>
export default {
  name: "home-menu",
  data() {
    return {
      parentMsg: this.$route.query.text,
      Meth: [],
      id: this.$route.query.id,
      Meun3: [],
      operatetype:'',   
    };
  },
  methods: {
     //获取url指定参数的值
      
    clickmeun3(name, id, url) {
       var theRequest = new Object();
           if (url.indexOf("?") != -1) {
               var str = url.substr(1);
               var strs = str.split("&");
               for (var i = 0; i < strs.length; i++) {
                   theRequest[strs[i].split("=")[0]] = unescape(strs[i].split("=")[1]);
               }
           }
       this.operatetype = theRequest['operatetype']
        this.$router.push({
            path:url,
          query: { name: name, id: id,url:url,operatetype:this.operatetype}
      })
    },
    getmenudata(){
        this.$http
            .post(
              "/API/Menuservice/AddTreeMenuChild",
              { "": this.id },
              { emulateJSON: true }
            )
            .then(res => {
              this.Meth = res.data;
            });
    }
  },
  created(){
    global.saodata="";
    this.getmenudata();
  },
  updated() {
     //进入对应foot小标高亮
    if(this.parentMsg == "资产报表"){
      this.$refs.echartactive.state()
    }else{
      this.$refs.echartactive.home()
    }
  },
    watch: {
    '$route' (to, from) {
      // react to route changes...
      this.parentMsg=to.query.text;
      this.id=to.query.id;
      this.getmenudata();
    }
    },
}
</script>
<style>
.HomemenuContent {
  width: 100%;
  height: 80%;
  background: #fafafb;
  padding-top: 20px;
  margin-bottom: 65px;
  overflow-y: auto;

}
.HomemenuContent ul li {
  width: 95%;
  height: auto;
  line-height: 45px;
  font-size: 18px;
  margin-left:3%;
  list-style-type: none;
}
.HomemenuContent ul li .Jugespan {
  width: 95%;
  height: 100%;
  display: inline-block;
  font-size:22px;
  font-weight: 800;
  margin-left:10px;
}
.LevelMenu {
  width: 98%;
  display:flex;
    align-items: center;
    flex-wrap: wrap;
    padding-left:5px;
    border-bottom:1px solid #ccc;/*no*/
}
.HomemenuContent ul li:last-child .LevelMenu{
  border:none;
}
.HomemenuContent ul li .LevelMenu li {
  width: 30%;
  height: 120px;
  line-height: 40px;
  font-size: 14px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin:0;
  float: left;
  margin-left:1%;
}
.HomemenuContent ul li:last-child{
  margin-bottom:10%;
}
.MenuImg{
  width:50px;
  height:50px;
  margin-bottom:5px;
}
.Second{
  width:31%;
  display:flex;
    align-items: center;
    flex-wrap: wrap;
    padding-left:5px;
    float:left;
}
.Second .sanji{
  height: 120px;
  line-height: 40px;
  font-size: 14px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin:0;
  float: left;
    margin-left:9%;
}
</style>
