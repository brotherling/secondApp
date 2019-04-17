<template>

<section class="cutoff">
  <!-- <div class="cutoff_search" >
      <div class="sealeft"><span>{{titledata}}</span><img src="../assets/xiala.png" alt="" @click="showserial()"></div>
      <div class="searight">
            <el-input placeholder=""  class="input-with-select" v-model="boxvalue">
                    <el-button @click="clisearch()" slot="append" icon="el-icon-search" ></el-button>
                </el-input>
            <img @click="batchFlicking " class="RImg" src='../assets/shaoma.png'>
      </div>
  </div> -->
  <div class="cutoff_search">
            <div class="sealeft">{{titledata}}<span class="LeftImg" @click="showserial()"></span></div>
            <div class="searight">
                 <el-input placeholder=""  class="input-with-select" v-model="boxvalue">
                    <el-button slot="append" icon="el-icon-search" @click="clisearch()"></el-button>
                </el-input>
                <img class="RImg" src='../assets/shaoma.png' @click="batchFlicking()">
            </div>
        </div>
  <div class="barSeriation">
      <ul>
        <li @click="clicksea($event,ati.name,index)" v-for="(ati,index) in serial" :key="ati.name">{{ati.name}}<span class="seriationImg"></span></li>
      </ul>
  </div>
        <div id="zhezhao"></div>
</section>
</template>
<script>
    export default{
        name:"searchbar",
        props:{
            serial:"",
            clicksearch:Function,
            data:Object,
            titledata:"",
        },
        data(){
            return{
                boxvalue:"",
            }
        },
        methods:{
            showserial(){
                $(".barSeriation").toggle();
                $("#zhezhao").toggle();
            },
            clicksea(e,name,index){
                for(let i=0,data=$(".seriationImg").length;i<data;i++){
                    $(".seriationImg").eq(i).hide();
                }
                $(".seriationImg").eq(index).show();
                if(name=="全部"){
                    this.data.filter="";
                }else{
                this.data.filter=name;
                }
                this.clisearch();
                this.showserial();
            },
            clisearch(e){
                // console.log(e,this.boxvalue);
                if(e==undefined){
                   this.data.param=this.boxvalue
                }else{
                this.data.param=e;
                this.boxvalue=e
                }
                 this.clicksearch();
            },
            batchFlicking(){
                this.saoma(this.clisearch);
            }
        },
        mounted() {
            $(".seriationImg").eq(0).show();
        },
    }
</script>
<style>
.cutoff{
    width:100%;
    background: #fafafb;
    overflow: auto;
}
.cutoff .cutoff_search{
    width:100%;
    height:70px;
    border-bottom:1px solid #ccc;/*no*/
}
.sealeft {
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
.RImg {
  width: 30px;
  height: 30px;
  position: relative;
  top:13px;
  left:12px;
  float: left;
}
.searight {
  width: 56%;
  height: 53px;
  font-size: 24px;
  margin-left: 20px;
  display: block;
  float: left;
}

.searight .el-input-group {
  width: 80%;
  margin-top:12px;
  float: left;
}
.searight .el-icon-search {
  color: #039ee7;
}
.searight .el-button{
    padding:0;

}
.searight .el-icon-search{
margin-left:6px;
}
.searight .el-input-group__append{
    background:white;
}
.barSeriation{
    width:100%;
    display:none;
    background:white;
    z-index: 1000;
    position: fixed;
}
.barSeriation ul{
    width:100%;
}
.barSeriation ul li{
    width:80%;
    list-style-type: none;
    height: 55px;
    line-height: 55px;
    font-size:18px;
    margin-left:10%;
    border-bottom:1px dashed #E6E6E6;/*no*/
    padding:5px;
    position: relative;
}
.barSeriation ul li:last-child{
    border:none;
}
.seriationImg{
    width:25px;
    height: 25px;
    background:url("../assets/xuan.png") no-repeat;
    background-size:cover;
    position: absolute;
    right: 50px;
    top:10px;
    display:none;
}
#zhezhao{
    display: none;
    position: fixed;
    left: 0%;
    width: 100%;
    height: 65%;
    background-color: black;
    z-index:999;
    bottom:10%;
    -moz-opacity: 0.8;
    opacity:.50;
}
.el-input-group__append{
    border:1px solid #ccc;
    border-left:none;
    text-align: center
}
</style>

