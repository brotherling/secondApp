<template>
<div class="applyChoose">
  <v-header :header="parentMsg"></v-header>
    <section class="applyChooseContent">
        <div class="applyConetentTop">
            <div class="ConetentTopleft">{{this.descstring}}<span class="ConetentTopleftImg" @click="clickLeftImg()"></span></div>
            <div class="ConetentTopRight">
                 <el-input placeholder=""  class="input-with-select" v-model="SortingData">
                    <el-button slot="append" icon="el-icon-search" @click="sous()"></el-button>
                </el-input>
                <!-- <img class="RightImg" src='../assets/shaoma.png' @click="clicksao()"> -->
            </div>           
        </div>
        <div class="applySeriation">
          <ul>
                <li @click="clickseriation($event)">默认排序<span  class="applySeriationImg"></span></li>
                <li  @click="clickseriation($event)">编码正序<span class="applySeriationImg"></span></li>
                <li  @click="clickseriation($event)">编码倒序<span class="applySeriationImg"></span></li>
                <li  @click="clickseriation($event)">使用人员<span class="applySeriationImg"></span></li>
                <li  @click="clickseriation($event)">购置日期正序<span class="applySeriationImg"></span></li>
                <li  @click="clickseriation($event)">购置日期倒序<span class="applySeriationImg"></span></li>
            </ul>
        </div>
        <div class="edit_applypagedata" id="assetpage" @scroll="handleScroll($event)">
            <div class="applypagedata" @click="clickselect(item,item.ASM_A1_10,$event)" v-for="item in applycsdata" :id="item.ASM_A1_10" :key="item.ASM_A1_10">
                <div class="applypagedata_left">
                    <icon id="selectImg" type="circle"></icon>
                    <icon id="selectImgSuccess" type="success" style="display:none;"></icon>
                </div>
                <div class="applypagedata_right">
                    <div class="pagedata_conttop"><span>资产编号:</span><span class="bian">{{item.ASM_A1_20}}</span></div>
                    <div class="pagedata_showdata">
                        <div><span>资产名称:</span><span class="void">{{item.ASM_A1_50}}</span></div>
                        <div><span>资产分类:</span><span class="void">{{item.ASM_A1_60}}</span></div>
                        <div><span>规格型号:</span><span class="void">{{item.ASM_A1_210}}</span></div>
                        <div v-if="item.ASM_A1_120=='在用'" class="showImg1"></div>
                        <div v-else-if="item.ASM_A1_120=='在库'" class="showImg2"></div>
                        <div v-else-if="item.ASM_A1_120=='维修'" class="showImg3"></div>
                        <div v-else-if="item.ASM_A1_120=='借用'" class="showImg4"></div>
                        <div v-else-if="item.ASM_A1_120=='报废'" class="showImg5"></div>
                        <div v-else-if="item.ASM_A1_120=='待报废'" class="showImg6"></div>
                        <img v-if="item.imgUrl!=''&&item.imgUrl!=null" :src="item.imgUrl" v-preview="item.imgUrl" alt="" class="edit_stockImg" preview-title-enable="false" preview-nav-enable="false">
                    </div>
                </div>                
            </div>
        </div>  
    </section>
    <div class="applyChooseFoot">
        <el-button type="primary" @click="arrfim">确认</el-button>
    </div>

    <div id="zhezhao"></div>
</div>
</template>
<script>
export default {
  name: "applyChoose",
  data() {
    return {
      parentMsg: '选择资产',
      applycsdata: [],
      fielddata: [],
      SortingData: "",
      page: 1,
      scloll: "true",
      num: "",
      sort:"ASM_A1_20",
      order:"desc",
      descstring:"默认排序",
      Imgshow:"false",
      operatetype: this.$route.query.operatetype,
      menuId:this.$route.query.id,
      url: this.$route.query.url,
      applycsId:this.$route.query.applycsId,
      chooseData:this.$route.query.chooseData,
      transmitID: [],
      transmitData: [],
    };
  },
  methods: {
    clickselect(chooseData,applycsId,e){ //切换选择点图片和获取id
        let arr = this.applycsId
        let dataArr = this.chooseData
        if(arr.indexOf(applycsId)==-1){
          arr.push(applycsId)
          dataArr.push(chooseData)
        }else{
          arr.splice($.inArray(applycsId,arr),1);
          dataArr.splice($.inArray(applycsId,arr),1)
        }
        let dom=e.currentTarget;
        $(dom).find("#selectImg").toggle();
        $(dom).find("#selectImgSuccess").toggle();
    },
    clicksao(){
      this.saoma(this.sous)
    },
    sous(e){
      if(e!=undefined){
      this.SortingData=e
      }
      this.applycsId=[];
        this.page=1;
        this.applycsdata=[];
        this.getloadData();
    },
    getloadData() {
      this.scloll = false;
      this.$http
        .post(
          "/API/apply/GetChooseAssetList",
          {
              menuId: this.menuId,
              operatetype: this.operatetype,
              pageNumber: this.page,
              pageSize: "10",
              sortName: this.sort,
              order: this.order,
              url: this.url,
              param: this.SortingData,
              ids : this.applycsId,
          },
          { emulateJSON: true }
        )
        .then(res => {
          let data = JSON.parse(res.data);
          this.num = data.total;
           this.scloll = true;
          data.rows.forEach(function(e) {
            this.applycsdata.push(e);
          }, this);
        });
    },
    handleScroll(e) {
      let $el = e.target;
      if ($el.scrollTop + $el.offsetHeight == $el.scrollHeight || $el.scrollTop + $el.offsetHeight == $el.scrollHeight-1) {
        this.page += 1;
        if (this.page > (this.num / 10+2)) {
          this.$message({
            message: "数据已经全部加载完",
            type: "warning"
          });
          return;
        }
        if (this.scloll) {
          this.getloadData();
        }
      }
    },
    clickseriation(e){
        let dom=e.target;
        let classimg=document.getElementsByClassName("applySeriationImg");
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
        let dom= document.getElementsByClassName("applySeriation");
        let zhedom =document.getElementById("zhezhao");
        let domtoggle= dom[0].style.display;
        if(domtoggle=="none"){
        dom[0].style.display="block";
        zhedom.style.display="block";
        }else{
        dom[0].style.display="none";
        zhedom.style.display="none";
        }
    },
    //确认按钮
    arrfim(){
        //回到上一页,只有确定返回才传值
        this.transmitID = this.applycsId
        this.transmitData = this.chooseData
        $(".restoring").trigger("click");
    //    this.$router.push({ path:'/applyAdd',query: {ids:this.applycsId,csData:this.chooseData} })
    }
  },
  beforeRouteLeave(to, from, next) {
        from.query.ids = this.transmitID;
        from.query.csData = this.transmitData;
        // 设置下一个路由的 meta
        next();
    },
  mounted() {
    if(this.$route.query.saodata !=undefined){
        this.SortingData=this.$route.query.saodata;
    }
    this.getloadData();
        let classimg=document.getElementsByClassName("applySeriationImg");
        classimg[0].style.display="block";
  },
    watch:{
      '$route'(to,from){
          // react to route changes...
          this.parentMsg=to.query.name;
          this.menuId=to.query.id;
          this.url=to.query.url;
          this.sous()
      }
  }
};
</script>
<style>
.applyChoose{
    height: 100%;
}
.weui-icon-success{
  color:#0AA0E7 !important;
}
.edit_stockImg{
    width:65px;
    height: 70px;
    position: absolute;
    right: 25px;
    bottom:10px;
}
.pagedata_conttop{
    font-size:14px;/* no*/
    margin-top:3px;
}
.pagedata_conttop .bian{
     color:#666;
     margin-left: 15px;
}
.pagedata_showdata{
    margin-top:3px;
}
.pagedata_showdata div{
    line-height: 28px;
    color:#989898;
    letter-spacing: 2px;
    font-size: 12px;/* no*/
}
.pagedata_showdata .void{
  color:#000;
  margin-left:12px;
}
.applyChooseContent {
  width: 100%;
  height: 78%;
  background: #fafafb;

}
.applyConetentTop {
  width: 100%;
  height: 70px;
  border-bottom: 1px solid #ccc;/*no*/
}
.ConetentTopleft {
  width: 35%;
  height: 40px;
  font-size: 19px;
  margin-left: 16px;
  border-right: 1px solid #999; /*no*/
  display: block;
  float: left;
  margin-top:15px;
}
.ConetentTopleftImg {
  background: url("../../assets/xiala.png") no-repeat;
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
.ConetentTopRight {
  width: 56%;
  height: 53px;
  font-size: 24px;
  margin-left: 20px;
  display: block;
    float: left;
}
.edit_applypagedata {
  /* border-top: 1px solid #ccc; */
  width: 100%;
  height:84%;
  padding-top: 20px;
  overflow-y:auto;
  /* padding-bottom: 5%; */
}
.applypagedata {
  margin-top: 20px;
  height: 115px;
    width: 95%;
    margin-left: 1%;
    display:flex;
}
.edit_applypagedata .applypagedata:nth-of-type(1){
  margin-top:5px;
}
.applypagedata_left{
    flex:1;
    display: flex;
    align-items: center;
}
.applypagedata_right{
  border: 0px solid #ccc;
  border-radius: 5px;
  box-shadow:1px 1px 1px 2px #eee; /* no*/
  background: white;
  overflow: hidden;
  position: relative;
  flex:10;
  padding-left:10px;
}
.Showmore {
  position: absolute;
  right: 10px;
  bottom: 5px;
  font-size: 14px;
  color: #039ee7;
}
.ConetentTopRight .el-input-group {
  width: 80%;
  margin-top:12px;
  float: left;
}
.ConetentTopRight .el-input-group__append {
  padding: 0 10px;
  background: white;
}
.ConetentTopRight .el-icon-search {
  color: #039ee7;
}
.showImg1 {
  position: absolute;
  top: 0;
  right: 0;
  width: 60px;
  height: 25px;
  background: url("../../assets/zaiyong.png") no-repeat;
  background-size: cover;
}
.showImg2 {
  position: absolute;
  top: 0;
  right: 0;
  width: 60px;
  height: 25px;
  background: url("../../assets/zaiku.png") no-repeat;
  background-size: cover;
}
.showImg3 {
  position: absolute;
  top: 0;
  right: 0;
  width: 60px;
  height: 25px;
  background: url("../../assets/weixiu.png") no-repeat;
  background-size: cover;
}
.showImg4 {
  position: absolute;
  top: 0;
  right: 0;
  width: 60px;
  height: 25px;
  background: url("../../assets/jieyong.png") no-repeat;
  background-size: cover;
}
.showImg5 {
  position: absolute;
  top: 0;
  right: 0;
  width: 60px;
  height: 25px;
  background: url("../../assets/baofei.png") no-repeat;
  background-size: cover;
}
.showImg6 {
  position: absolute;
  top: 0;
  right: 0;
  width: 60px;
  height: 25px;
  background: url("../../assets/daibaofei.png") no-repeat;
  background-size: cover;
}
.applySeriation{
    width:100%;
    background-color: white;
    position: fixed;
    display:none;
    z-index: 1000;
}
.applySeriation ul{
    width:100%;
}
.applySeriation ul li{
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
.applySeriation ul li:last-child{
    border:none;
}
.applySeriationImg{
    width:25px;
    height: 25px;
    background:url("../../assets/xuan.png") no-repeat;
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
    height: 50%;
    background-color: black;
    z-index:999;
    bottom:8%;
    -moz-opacity: 0.8;
    opacity:.80;
    filter: alpha(opacity=80);
}
.Amendfont{
    width:120px;
    text-align: right;
    display:inline-block;
}
.DataImg{
  width:160px;
  height:110px;
  margin-left:15px;
  margin-top:3px 0;
  vertical-align:middle;
}
.applyChooseFoot{
    height: 9%;
    display: flex;
    justify-content: space-around;
    align-items:center;
}
.applyChooseFoot .el-button{
    width: 40%;
}
</style>
