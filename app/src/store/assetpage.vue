<template>
<div class="div1">
  <v-header :header="parentMsg"></v-header>
    <section class="assetcontent" @scroll="handleScroll($event)">
        <div class="conetenttop">
            <div class="Topleft">{{this.descstring}}<span class="LeftImg" @click="clickLeftImg()"></span></div>
            <div class="TopRight">
                 <el-input placeholder=""  class="input-with-select" v-model="SortingData">
                    <el-button slot="append" icon="el-icon-search" @click="sous()"></el-button>
                </el-input>
                <img class="RightImg" src='../assets/shaoma.png' @click="clicksao()">
            </div>
        </div>
        
        <div class="seriation">
          <ul>
                <li @click="clickseriation($event)">默认排序<span  class="seriationImg"></span></li>
                <li  @click="clickseriation($event)">编码正序<span class="seriationImg"></span></li>
                <li  @click="clickseriation($event)">编码倒序<span class="seriationImg"></span></li>
                <li  @click="clickseriation($event)">使用人员<span class="seriationImg"></span></li>
                <li  @click="clickseriation($event)">购置日期正序<span class="seriationImg"></span></li>
                <li  @click="clickseriation($event)">购置日期倒序<span class="seriationImg"></span></li>
            </ul>
        </div>
        <div class="statistics">
          <div><span class="origin">总数量:</span>{{quantity}}</div>
          <div><span class="origin">总原值:</span>{{assetOriWorth}}</div>
        </div>
        <div class="assetpagedata" id="assetpage">
            <div class="pagedata" v-for="item in assetedata" :id="'a'+item.ASM_A1_10" :key="item.ASM_A1_10">
                <ul  @click="showdata(item.ASM_A1_10)">
                    <li v-for="(tou,index) in headtdata" :key="tou.field">
                      <span class="Amendfont">{{tou.title}}:</span><span class="renderData">{{renderData(tou.field,item)}}</span>
                    </li>
                    <li  v-if="item.imgUrl!=''" class="lastli">
                      <span class="Amendfont">图片信息</span>
                      <img class="DataImg" v-preview="item.imgUrl" :src="item.imgUrl" preview-title-enable="false" preview-nav-enable="false">
                    </li>
                </ul>
                <div v-if="item.ASM_A1_120=='在用'" class="showImg1"></div>
                <div v-else-if="item.ASM_A1_120=='在库'" class="showImg2"></div>
                <div v-else-if="item.ASM_A1_120=='维修'" class="showImg3"></div>
                <div v-else-if="item.ASM_A1_120=='借用'" class="showImg4"></div>
                <div v-else-if="item.ASM_A1_120=='报废'" class="showImg5"></div>
                <div v-else-if="item.ASM_A1_120=='待报废'" class="showImg6"></div>
            </div>
        </div>
    </section>
    <div id="zhezhao"></div>
  <v-foot @refreshbizlines="clicksao" ref="assetactive"></v-foot>
</div>
</template>
<script>
export default {
  name: "assetpage",
  data() {
    return {
      parentMsg: this.$route.query.name,
      assetedata: [],
      headtdata: [],
      fielddata: [],
      SortingData: "",
      page: 1,
      scloll: "true",
      num: "",
      sort:"ASM_A1_20",
      order:"desc",
      descstring:"默认排序",
      Imgshow:"false",
      menuId: this.$route.query.id,
      url: this.$route.query.url,
      quantity:"",
      assetOriWorth:""
    };
  },
  methods: {
    clicksao(){
      this.saoma(this.sous)
    },
    EnlargeImg(){
      event.stopPropagation();
    },
    renderData(name,data) {
      let rows = data[name];
      return rows;
    },
    showdata(id) {
      let dom = document.getElementById("a" + id);
      let childElements = document.getElementsByClassName("pagedata");
      let toggle = dom.style.height;
      if (toggle == "auto") {
        dom.style.height = "2.555556rem";
      } else {
        for (let i = 0; i < childElements.length; i++) {
          childElements[i].style.height = "2.555556rem";
        }
        dom.style.height = "auto";
      }
    },
    sous(e){
      if(e!=undefined){
      this.SortingData=e
      }
        this.page=1;
        this.assetedata=[];
        this.UploadData();
    },
    UploadData() {
      this.scloll = false;
      this.$http
        .post(
          "/API/assetservice/GetAssetList",
          {
              menuId: this.menuId,
              pageNumber: this.page,
              pageSize: "10",
              sortName: this.sort,
              order: this.order,
              url: this.url,
              param: this.SortingData
          },
          { emulateJSON: true }
        )
        .then(res => {
          let data = JSON.parse(res.data);
          this.num = data.total;
          if(data.rows[0]){
            this.quantity=data.rows[0].quantity;
            this.assetOriWorth=data.rows[0].assetOriWorth;
          }else{
            this.quantity = parseFloat(this.quantity) + 0;
            var num = parseFloat(this.assetOriWorth) + 0.00;
            //保留两位小数
            this.assetOriWorth =num.toFixed(2);
          }
          
          data.rows.forEach(function(e) {
            this.assetedata.push(e);
          }, this);
        });
        this.$http
        .get("/API/assetservice/GetAssetTitle", {params:{menuId:this.menuId}}, { emulateJSON: true })
        .then(res => {
          let data = JSON.parse(res.data);
                this.Imgshow=true;
          this.scloll = true;
           this.headtdata=data;
        });
    },
    handleScroll(e) {
      let $el = e.target;
      if ($el.scrollTop + $el.offsetHeight == $el.scrollHeight) {
        this.page += 1;
        if (this.page > (this.num / 10+2)) {
          this.$message({
            message: "数据已经全部加载完",
            type: "warning"
          });
          return;
        }
        if (this.scloll) {
          this.UploadData();
        }
      }
    },
    clickseriation(e){
        let dom=e.target;
        let classimg=document.getElementsByClassName("seriationImg");
        let text =dom.innerText;
        let sort,order;
        for(let i=0;i<classimg.length;i++){
            classimg[i].style.display="none";
        }
        dom.children[0].style.display="block";
        switch(text)
        {
            case "编码正序":sort="ASM_A1_20";order="asc"; break;
            case "编码倒叙":sort="ASM_A1_20";order="desc";break;
            case "使用人员":sort="useUserId" ;order="desc";break;
            case "购置日期正序":sort="ASM_A1_290";order="asc";break;
            case "购置日期倒序":sort="ASM_A1_290";order="desc";break;
            default:sort="ASM_A1_20";order="desc";break;
        }
        this.descstring=text;
        this.sort=sort;this.order=order;
        this.sous();
        this.clickLeftImg();
    },
    clickLeftImg(){
        let dom= document.getElementsByClassName("seriation");
        let zhedom =document.getElementById("zhezhao");
        let domtoggle= dom[0].style.display;
        if(domtoggle=="none"){
        dom[0].style.display="block";
        zhedom.style.display="block";
        }else{
        dom[0].style.display="none";
        zhedom.style.display="none";
        }
    }
  },
  mounted() {
    //进入对应foot小标高亮
    if(this.parentMsg == '个人资产查询'){
      this.$refs.assetactive.person()
    }else{
      this.$refs.assetactive.home()
    }
    if(this.$route.query.saodata !=undefined){
      this.SortingData=this.$route.query.saodata;
    }
    this.UploadData();
    let classimg=document.getElementsByClassName("seriationImg");
    classimg[0].style.display="block";
  },
  watch:{
    '$route'(to,from){
        // react to route changes...
        this.parentMsg=to.query.name;
        this.menuId=to.query.id;
        this.url=to.query.url;
        if(to.query.name=="管辖资产查询"&& to.query.saodata!=undefined){
          this.sous(to.query.saodata)
        }else{
          this.sous()
        }
    }
}
};
</script>
<style>
.statistics{
  display:flex;
  justify-content:space-around;
  align-items: center;
  font-size:20px;
  height:35px;
  font-weight:700;
  letter-spacing: 1px; 
}
.statistics .origin{
  font-weight:400;
  font-size:16px;
  margin-right: 8px;
}
.assetcontent {
  width: 100%;
  height: 80%;
  background: #fafafb;
  /* padding-top: 20px; */
  /* margin-bottom: 50%; */
  overflow: auto;
}
.conetenttop {
  width: 100%;
  height: 70px;
  /* display: flex;
  align-items: center; */
}
.Topleft {
  width: 35%;
  height: 40px;
  font-size: 19px;
  margin-left: 16px;
  border-right: 1px solid #999; /*no*/
  display: block;
  float: left;
  margin-top:15px;
}
.LeftImg {
  background: url("../assets/xiala.png") no-repeat;
  width: 25px;
  height: 25px;
  display: inline-block;
  background-size: cover;
  position: relative;
  top: 5px;
  left: 6px;
}
.RightImg {
  width: 30px;
  height: 30px;
  position: relative;
  top:13px;
  left:12px;
  float: left;
}
.TopRight {
  width: 56%;
  height: 53px;
  font-size: 24px;
  margin-left: 20px;
  display: block;
    float: left;
}
.assetpagedata {
  margin-top: 5px;
  border-top: 1px solid #ccc;/*no*/
  width: 100%;
  margin-bottom: 15%;
}
.pagedata {
  margin-top: 20px;
  height: 115px;
  width: 90%;
  margin-left: 5%;
  border: 0px solid #ccc;
  border-radius: 5px;
  box-shadow: 1px 1px 10px 5px #eee; /* no*/
  background: white;
  overflow: hidden;
  position: relative;
  letter-spacing: 1px; 
}
.pagedata ul li {
  /* height: 35px; */
  line-height: 35px;
  font-size: 16px;
  list-style-type: none;
  margin-left: 10px;
}
.pagedata ul li:first-child {
  margin-top: 6px;
}
.pagedata ul .lastli{
  height: 120px;
  line-height: 120px;
  border-top:1px dashed #E6E6E6;/*no*/
}
.Showmore {
  position: absolute;
  right: 10px;
  bottom: 5px;
  font-size: 14px;
  color: #039ee7;
}
.TopRight .el-input-group {
  width: 80%;
  margin-top:12px;
  float: left;
}
.TopRight .el-input-group__append {
  padding: 0 10px;
  background: white;
}
.TopRight .el-icon-search {
  color: #039ee7;
}
.showImg1 {
  position: absolute;
  top: 0;
  right: 0;
  width: 60px;
  height: 25px;
  background: url("../assets/zaiyong.png") no-repeat;
  background-size: cover;
}
.showImg2 {
  position: absolute;
  top: 0;
  right: 0;
  width: 60px;
  height: 25px;
  background: url("../assets/zaiku.png") no-repeat;
  background-size: cover;
}
.showImg3 {
  position: absolute;
  top: 0;
  right: 0;
  width: 60px;
  height: 25px;
  background: url("../assets/weixiu.png") no-repeat;
  background-size: cover;
}
.showImg4 {
  position: absolute;
  top: 0;
  right: 0;
  width: 60px;
  height: 25px;
  background: url("../assets/jieyong.png") no-repeat;
  background-size: cover;
}
.showImg5 {
  position: absolute;
  top: 0;
  right: 0;
  width: 60px;
  height: 25px;
  background: url("../assets/baofei.png") no-repeat;
  background-size: cover;
}
.showImg6 {
  position: absolute;
  top: 0;
  right: 0;
  width: 60px;
  height: 25px;
  background: url("../assets/daibaofei.png") no-repeat;
  background-size: cover;
}
.seriation{
    width:100%;
    background-color: white;
    position: fixed;
    display:none;
    z-index: 1000;
}
.seriation ul{
    width:100%;
}
.seriation ul li{
    width:80%;
    list-style-type: none;
    height: 40px;
    line-height: 40px;
    font-size:18px;
    margin-left:10%;
    border-bottom:1px dashed #E6E6E6;/*no*/
    padding-left:5px;
    position: relative;
}
.seriation ul li:last-child{
    border:none;
}
.seriationImg{
    width:25px;
    height: 25px;
    background:url("../assets/xuan.png") no-repeat;
    background-size:cover;
    position: absolute;
    right: 20px;
    top:6px;
    display:none;
}
#zhezhao{
    display: none;
    position: fixed;
    left: 0%;
    width: 100%;
    height: 55%;
    background-color: black;
    z-index:999;
    bottom:8%;
    -moz-opacity: 0.8;
    opacity:.80;
    filter: alpha(opacity=80);
}
.Amendfont{
    float: left;
    width:120px;
    text-align: right;
    display:inline-block;
    color:#999;   
}
.renderData{
  margin-left: 12px;
  display:inline-block;
  width: 66%;
}
.DataImg{
  width:160px;
  height:110px;
  margin-left:15px;
  margin-top:3px 0;
  vertical-align:middle;
}
</style>
