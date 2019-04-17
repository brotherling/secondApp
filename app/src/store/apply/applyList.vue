<template>
    <div class="applylist">
        <v-header :header="header"></v-header>
        <div class="applyListContent">
            <div class="applyListContentTop">
               <tab :line-width="1" custom-bar-width="60px">
                    <tab-item selected @on-item-click="onItemClick" active-class="active-1-1">待审核({{newAddCount}})</tab-item>
                    <tab-item @on-item-click="onItemClick" active-class="active-1-1">待签字({{noSignCount}})</tab-item>
                    <tab-item @on-item-click="onItemClick" active-class="active-1-1">领用单</tab-item>
                </tab>
            </div>
            <div class="applyListContentMid">
                <section class="applyListData">
                    <div class="applyListDataTop">
                        <div class="ListDataleft">{{filterTitle}}<span class="ListDataleftImg" @click="clickLeftImg()"></span></div>
                        <div class="ListDataRight">
                            <el-input placeholder=""  class="input-with-select" v-model="SortingData">
                                <el-button slot="append" icon="el-icon-search" @click="sous()"></el-button>
                            </el-input>
                        </div>           
                    </div>
                    <div class="applySeriation">
                        <ul>
                           <li @click="clicksel($event,ati.name,index)" v-for="(ati,index) in serialdata" :key="ati.name">{{ati.name}}<span class="applylistSeriationImg"></span></li>
                        </ul>
                    </div>
                    <div class="editapplyListDataMid" @scroll="handleScroll($event)">
                        <div class="applyListDataMid" v-for="item in applylist" :id="item.ASM_A1_10" :key="item.ASM_A1_10">
                            <div class="applyListDataMid_left" @click="clickchoose(item.ASM_B2_10,$event)" v-show="chooseFlag">
                                <icon id="selectImg" type="circle"></icon>
                                <icon id="selectImgSuccess" type="success" style="display:none;"></icon>
                            </div>
                            <div class="applyListDataMid_right" @click="toApplyBill(item.ASM_B2_10, item.ASM_B2_80)">
                                <div class="ListData_conttop"><span>单据编号:</span><span class="bian">{{item.ASM_B2_60}}</span></div>
                                <div class="ListData_showdata">
                                    <div><span>领用人:</span><span class="void">{{item.str30Name}}</span></div>
                                    <div><span>领用部门:</span><span class="void">{{item.str180Name}}</span></div>
                                    <div><span>领用日期:</span><span class="void">{{item.ASM_B2_40}}</span></div>
                                    <div v-if="item.ASM_B2_80=='104001'" class="showImg1">新增</div>
                                    <div v-else-if="item.ASM_B2_80=='104002'" class="showImg1">完成</div>
                                    <div v-else-if="item.ASM_B2_80==''" class="showImg1"></div>
                                </div>
                            </div>                
                        </div>
                    </div>  
                </section>
                <div class="applyListFoot">
                    <el-button type="primary" @click="applyListDel" v-show="footFlag">删除</el-button>
                </div>
                <div id="zhezhao"></div> 
            </div>
        </div>
    </div>
</template>
<script>
export default {
    name:'applyList',
    data(){
        return {
            header: '领用处理',
            serialdata: [{name:"全部"},{name:"新增"},{name:"完成"},{name:"已签字"}],
            num: '',
            page: 0,
            RecordCount: '', //数据总数量
            scloll: "true",
            applylist: [], //总数据
            applylsId: [],
            SortingData: '', //搜索
            filter: '',
            filterTitle: '全部',
            menuId:this.$route.query.id,
            footFlag: true,
            chooseFlag: true,
            newAddData: [], //新增数据
            newAddCount: '', //新增数量
            noSignData: [], //待签数据
            noSignCount: '', //待签数量
            pageIndex: 0, //为不同的跳转准备
        }
    },
    methods: {
        onItemClick(index){
            this.pageIndex = index
          if(index == 0){
            this.filter = "{\"ASM_B2_80\":\"104001\"}"
            this.RecordCount = '';
            this.page=0
            this.applylist = []
            this.getlistData()
            this.footFlag = true
            this.chooseFlag = true
          }else if(index == 1){
            this.filter = "{\"ASM_B2_270\":\"待签\",\"ASM_B2_80\":\"104002\"}"
            this.RecordCount = ''
             this.page=0
            this.applylist = []
            this.getlistData()
            this.footFlag = false
            this.chooseFlag = false
          }else{
            this.filter = ""
            this.RecordCount = ''
            this.page=0
            this.applylist = []
            this.getlistData()
            this.footFlag = false
            this.chooseFlag = false
          }
        },
        //跳转到单据审核
        toApplyBill(Id, status){
            if(status == '104001' && this.pageIndex == 2){
                this.$router.push({ path:'/applyAdd' ,query: {editId: Id, status: status, pageIndex: this.pageIndex}})
            }else{
                this.$router.push({ path:'/applyBill' ,query: {id: Id, status: status, pageIndex: this.pageIndex}})
            }
            
        },
        clickchoose(applylsId,e){ //切换选择点图片和获取id
            let arr = this.applylsId
            if(arr.indexOf(applylsId)==-1){
                arr.push(applylsId)
            }else{
                arr.splice($.inArray(applylsId,arr),1);
            }
            let dom=e.currentTarget;
                $(dom).find("#selectImg").toggle();
                $(dom).find("#selectImgSuccess").toggle();
        },
        sous(e){
            if(e!=undefined){
                this.SortingData=e
            }
            this.applylsId=[];
            this.page=0;
            this.applylist=[];
            this.getlistData();
        },
        getlistData(){
            this.scloll = false;
            let Data = {
                menuId: this.menuId,
                pageNumber: this.page,
                pageSize: "10",
                sortName: "ASM_B2_60",
                order:"desc",
                param: this.SortingData,
                filter: this.filter
            }
            this.$http
                .get(
                "API/apply/GetApplyList",
                {params:{data:JSON.stringify(Data)}},
                { emulateJSON: true }
                )
                .then(res => {
                    this.RecordCount = res.body.RecordCount
                    this.scloll = true;
                    if(this.filter == "{\"ASM_B2_80\":\"104001\"}"){
                        this.newAddCount = res.body.RecordCount
                    }else if(this.filter == "{\"ASM_B2_270\":\"待签\",\"ASM_B2_80\":\"104002\"}"){
                        this.noSignCount = res.body.RecordCount
                    }
                    res.body.List.forEach(function(e) {
                        this.applylist.push(e);
                    }, this);
                });
        },
        handleScroll(e) {
        let $el = e.target;
        let num = this.page
        if ($el.scrollTop + $el.offsetHeight == $el.scrollHeight || $el.scrollTop + $el.offsetHeight == $el.scrollHeight-1) {
            num += 10;
            if (num > (this.RecordCount) + 10) {
                this.$message({
                    message: "数据已经全部加载完",
                    type: "warning"
                });
                return;
            }
            if (this.scloll) {
                this.page = num
                this.getlistData();
            }
        }
        },
        clicksel(e,name,index){
           for(let i=0,data=$(".applylistSeriationImg").length;i<data;i++){
                $(".applylistSeriationImg").eq(i).hide();
            }
            $(".applylistSeriationImg").eq(index).show();
            if(name=="全部"){
                this.filter="";
            }else{
                this.filterTitle = name
                if(name == '新增'){
                    this.filter = "{\"ASM_B2_80\":\"104001\"}"
                }else{
                    this.filter = "{\"ASM_B2_270\":\"待签\",\"ASM_B2_80\":\"104002\"}"
                }
            }
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
        //删除按钮
        applyListDel(){
            this.$http
            .post(
            "API/apply/Delete",
            { id:this.applylsId},{ emulateJSON: true })
            .then(res => {
                this.applylsId=[];
                this.page=0;
                this.applylist=[];
                this.getlistData()
            });
        }
    },
    created() {
        this.filter = "{\"ASM_B2_80\":\"104001\"}"
        this.getlistData()
    },
}
</script>
<style>
    .applylist{
        height: 100%;
    }
    .applylist .active-1-1{
        color: rgb(55, 174, 252) !important;
        border-color: rgb(55, 174, 252) !important;
    }
    .applyListContent{
         height: 87%;
    }
    .applyListContentMid{
       height: 100%;
    }
    .weui-icon-success{
        color:#0AA0E7 !important;
    }
    .ListData_conttop{
        font-size:14px;/* no*/
        margin-top:3px;
    }
    .ListData_conttop .bian{
        color:#666;
        margin-left: 15px;
    }
    .ListData_showdata{
        margin-top:3px;
    }
    .ListData_showdata div{
        line-height: 28px;
        color:#989898;
        letter-spacing: 2px;
        font-size: 12px;/* no*/
    }
    .ListData_showdata .void{
        color:#000;
        margin-left:12px;
    }
    .applyListData {
        width: 100%;
        height: 81%;
        background: #fafafb;

    }
    .applyListDataTop {
        width: 100%;
        height: 70px;
        border-bottom: 1px solid #ccc;/*no*/
    }
    .ListDataleft {
        width: 35%;
        height: 40px;
        font-size: 19px;
        margin-left: 16px;
        border-right: 1px solid #999; /*no*/
        display: block;
        float: left;
        margin-top:15px;
    }
    .ListDataleftImg {
        background: url("../../assets/xiala.png") no-repeat;
        width: 25px;
        height: 25px;
        display: inline-block;
        background-size: cover;
        position: relative;
        top: 5px;
        left: 6px;
    }
    .ListDataRight {
        width: 56%;
        height: 53px;
        font-size: 24px;
        margin-left: 20px;
        display: block;
        float: left;
    }
    .editapplyListDataMid {
    /* border-top: 1px solid #ccc; */
        width: 100%;
        height:86%;
        padding-top: 20px;
        overflow-y:auto;
    /* padding-bottom: 5%; */
    }
    .applyListDataMid {
        margin-top: 20px;
        height: 115px;
        width: 95%;
        margin-left: 1%;
        display:flex;
    }
    .editapplyListDataMid .applyListDataMid:nth-of-type(1){
        margin-top:5px;
    }
    .applyListDataMid_left{
        flex:1;
        display: flex;
        align-items: center;
    }
    .applyListDataMid_right{
        border: 0px solid #ccc;
        border-radius: 5px;
        box-shadow:1px 1px 1px 2px #eee; /* no*/
        background: white;
        overflow: hidden;
        position: relative;
        flex:10;
        padding-left:10px;
    }
    .ListData_showdata .showImg1 {
        position: absolute;
        top: 0;
        right: 0;
        width: 60px;
        height: 25px;
        text-align: center;
        color: #fff;
        background-color: #0AA0E7;
    }
    .Showmore {
        position: absolute;
        right: 10px;
        bottom: 5px;
        font-size: 14px;
        color: #039ee7;
    }
    .ListDataRight .el-input-group {
        width: 80%;
        margin-top:12px;
        float: left;
    }
    .ListDataRight .el-input-group__append {
        padding: 0 10px;
        background: white;
    }
    .ListDataRight .el-icon-search {
        color: #039ee7;
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
    .applylistSeriationImg{
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
    .applyListFoot{
        height: 9%;
        display: flex;
        justify-content: space-around;
        align-items:center;
    }
    .applyListFoot .el-button{
        width: 40%;
    }
</style>