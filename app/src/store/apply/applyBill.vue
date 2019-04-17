<template>
    <div class="applybill">
        <v-header :header="header"></v-header>
        <div class="applyBillContent">
           <div class="applyBillContentTop">
               <tab :line-width="1" custom-bar-width="60px">
                    <tab-item selected @on-item-click="itemClick" active-class="active-1-1">单据信息</tab-item>
                    <tab-item @on-item-click="itemClick" active-class="active-1-1">单据日志</tab-item>
                </tab>
            </div>
            <div class="applyBillConMidOne" v-if="contentFlag">
                <div class="applyBillConList">
                    <div class="applyBillData">
                        <p><span>单据编号:</span><span class="pLeft">{{billDataApply.ASM_B2_60}}</span></p>
                        <p><span>领用人:</span><span class="pLeft">{{billDataApply.str30Name}}</span></p>
                        <p><span>领用部门:</span><span class="pLeft">{{billDataApply.str180Name}}</span></p>
                        <p><span>领用日期:</span><span class="pLeft">{{billDataApply.ASM_B2_40}}</span></p>
                        <p><span>资产用途:</span><span class="pLeft">{{billDataApply.ASM_B2_90}}</span></p>
                        <p><span>存放地点:</span><span class="pLeft">{{billDataApply.ASM_B2_100}}</span></p>
                    </div>
                    <div class="applyBillData" v-show="billdataFlag">
                        <p><span>操作人员:</span><span class="pLeft">{{billDataApply.str210Name}}</span></p>
                        <p><span>操作部门:</span><span class="pLeft">{{billDataApply.str180Name}}</span></p>
                    </div>
                    <div class="applyBillData">
                        <p><span>附件:</span><span class="pLeft">{{billFile.fileName}}</span></p>
                    </div>
                </div>
                <div class="applyBillConDetail">
                    <div class="billdata" v-for="(item, index) in billDataList" :key="item.ASM_A1_10">
                        <p class="billdataHead">
                            <span class="detail">资产明细({{index+1}})</span>
                        </p>
                        <ul>
                            <li>
                                <span class="rendertTil">资产编号:</span>
                                <span class="renderShu">{{item.ASM_A1_20}}</span>
                            </li>
                            <li>
                                <span class="rendertTil">资产名称:</span>
                                <span class="renderShu">{{item.ASM_A1_50}}</span>
                            </li>
                            <li>
                                <span class="rendertTil">资产分类:</span>
                                <span class="renderShu">{{item.ASM_A1_60}}</span>
                            </li>
                            <li>
                                <span class="rendertTil">规格型号:</span>
                                <span class="renderShu">{{item.ASM_A1_210}}</span>
                            </li>
                        </ul>
                    </div>
                </div>
                <div id="signCanvas" v-show="signFlag">
                    手写签名
                </div>
            </div>
            <div class="applyBillConMidTwo" v-else>
                <timeline>
                     <timeline-item v-if=" this.daiyLog.Sign.People">
                        <h4><span class="itemSpan">{{daiyLog.Sign.People}}</span> <span>已签名</span></h4>
                        <p>{{daiyLog.Sign.Time}}</p>
                    </timeline-item>
                    <timeline-item v-if="this.daiyLog.Check.People">
                      <h4><span class="itemSpan">{{daiyLog.Check.People}}</span> <span>已审核</span></h4>
                        <p>{{daiyLog.Check.Time}}</p>
                    </timeline-item>
                    <timeline-item v-if="this.daiyLog.Add.People">
                      <h4><span class="itemSpan">{{daiyLog.Add.People}}</span> <span>新增单据</span></h4>
                        <p>{{daiyLog.Add.Time}}</p>
                    </timeline-item>
                </timeline>
            </div>
            <div class="applyBillConFoot" v-show="footFlag">
                <el-button type="primary" @click="billCheck()">审核</el-button>
            </div>
            <div class="applyBillConFoot" v-show="billsignFlag">
                <el-button type="primary" @click="signature()">签字</el-button>
            </div>
            <div class="applyBillConFoot" v-show="closeFlag">
                <el-button type="primary" @click="close()">关闭</el-button>
            </div>
        </div>
    </div>
</template>
<script>
var canvas = document.createElement("canvas");
var cxt = canvas.getContext("2d");
export default {
    data(){
        return {
            header: '领用处理',
            contentFlag: true,
            billDataApply: {},
            billDataList: [],
            billFile: {},
            billId: this.$route.query.id,
            footFlag: true,      //控制审核按钮是否显示 
            billsignFlag: false, //控制签字按钮是否显示
            billdataFlag: true,  //控制操作人员是否显示
            closeFlag: false,  //控制关闭按钮是否显示
            signFlag: false,   //控制签名框是否显示
            status: this.$route.query.status,
            billIndex: this.$route.query.pageIndex, //记录从哪个页面跳转过来的
            linewidth: 1, //线条粗细，选填
            color: "black", //线条颜色，选填
            background: "darkgrey", //线条背景，选填
            imgsrc: "", //签名图片
            daiyLog: {}, //单据日志
        }
    },
    methods: {
        //签字功能
        getCanvas() {
            this.$nextTick(() => {
                 let el = document.getElementById("signCanvas");
            el.appendChild(canvas);
            canvas.width = el.clientWidth;
            canvas.height = el.clientHeight;
            cxt.fillStyle = this.background; //填充绘图的背景颜色
            cxt.fillRect(0, 0, canvas.width, canvas.height); //绘制“已填色”的矩形
            cxt.strokeStyle = this.color; //笔触的颜色
            cxt.lineCap = "round"; //线条末端线帽的样式
            let linewidth = this.linewidth;
            //开始绘制
            canvas.addEventListener(
                "touchstart",
                function(e) {
                cxt.beginPath();
                cxt.lineWidth = linewidth; //当前线条的宽度，以像素计
                cxt.moveTo(e.changedTouches[0].pageX, e.changedTouches[0].pageY);
                }.bind(this),
                false
            );
            //绘制中
            canvas.addEventListener(
                "touchmove",
                function(e) {
                cxt.lineTo(e.changedTouches[0].pageX, e.changedTouches[0].pageY);
                cxt.stroke();
                }.bind(this),
                false
            );
            //结束绘制
            canvas.addEventListener(
                "touchend",
                function() {
                cxt.closePath();
                }.bind(this),
                false
            );
            })
           
        },
        handelColour() {
            this.color = "red";
            this.getCanvas();
        },
        handelThickness() {
            this.linewidth = 5;
            this.getCanvas();
        },
        handelClearEl() {
            cxt.clearRect(0, 0, canvas.width, canvas.height);
        },
        handelSaveEl() {
            let imgBase64 = canvas.toDataURL();
            console.log(imgBase64);
            this.imgsrc = imgBase64;
        },
        //切换显示页
        itemClick(index){
            if(index == 1){ //切换为单据日志页,按钮都隐藏
                this.contentFlag = false
                this.footFlag = false
                this.closeFlag = false
                this.billsignFlag = false
            }else{   //切换为单据页
                this.contentFlag = true
                 this.getCanvas()   //切换单据信息页显示签名框
                if(this.billIndex == 0){  //待审核新增过来为审核按钮
                    this.footFlag = true
                    this.billsignFlag = false
                    this.closeFlag = false
                }else if(this.billIndex == 1){ //代签字完成过来为签字按钮
                    this.footFlag = false
                    this.billsignFlag = true
                    this.closeFlag = false
                }else{                      //领用单完成过来为关闭按钮
                    this.footFlag = false
                    this.billsignFlag = false
                    this.closeFlag = true
                }
               
            }
        },
        getBillLog(){
            this.$http
            .get(
            "API/apply/GetDaiyLog",
            {params:{id:this.billId}},{ emulateJSON: true })
            .then(res => {
                if(res.status == 200){
                    this.daiyLog = res.body
                   
                }
            });
        },
        getBillData(){
            this.$http
            .get(
            "API/apply/Edit",
            {params:{id:this.billId}},{ emulateJSON: true })
            .then(res => {
                console.log(res)
                if(res.body.File.length != 0){
                    this.billFile = res.body.File[0]
                }
                this.billDataApply = res.body.apply
                this.billDataList = res.body.list
            });
        },
        //审核单据
        check(){
            let checkArr = [];
            checkArr.push(this.billId)
            this.$http
            .post(
            "API/apply/Confirm",
            { id:checkArr},{ emulateJSON: true })
            .then(res => {
                this.$router.go(-1)
            });
        },
        //审核单据按钮
        billCheck(){
            this.$confirm('是否确定审核此单据?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning',
            center: true
            }).then(() => {
            this.$message({
                type: 'success',
                message: '删除成功!',
                callback: this.check()
            });
            }).catch(() => {
            this.$message({
                type: 'info',
                message: '已取消删除'
            });
            });
        },
        // 关闭按钮
        close(){
            this.$router.go(-1)
        },
        // 签字按钮
        signature(){
            this.handelSaveEl()
            this.$http
            .post(
            "API/apply/SaveSign",
            { id: this.billId,img: this.imgsrc},{ emulateJSON: true })
            .then(res => {
               if(res.status == 200){
                    this.$router.go(-1)
               }
            });
        }
    },
    created() {
        this.getBillData()
        this.getBillLog() //获取日志信息
    },
    mounted() {
        if(this.status == '104002' && this.billIndex == 1){
            this.footFlag = false
            this.billsignFlag = true
            this.billdataFlag = false
            this.signFlag = true
        }else if(this.status == '104002' && this.billIndex == 2){
            this.footFlag = false
            this.billdataFlag = false
            this.closeFlag = true
        }
        if(this.signFlag == true){
             this.getCanvas()
        }
    },

}
</script>
<style>
    .applybill{
        height: 100%;
    }
    .applyBillContent{
        height: 87%;
        width: 100%;
    }
    .applyBillConMidOne{
        height: 86%;
        overflow-y: auto;
    }
    #signCanvas{
        width: 80%;
        height: 100px;/*no*/
        margin: 2% 10%;
    }
    #signCanvas canvas{
        display: block;
    }
    .applyBillConList{
        width: 80%;
        margin: 0 10%;
        padding: 2% 0;
    }
    .applyBillData p{
        line-height: 30px;
        letter-spacing: 1px;
    }
    .applyBillData .pLeft{
        margin-left: 10px;
    }
    .applyBillConDetail .billdata{
        margin-top: 10px;
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
    .billdataHead{
        width: 100%;
        height: 40px;
        line-height: 40px;
        border: 1px solid #666;
        background-color: #DCDFE6;
        padding: 0 20px 0 8px;
        margin-bottom: 5px;
    }
    .billdataHead .detail{
        float: left;
    }
    .billdataHead .applydel{
        float: right;
    }
    .billdata li{
        line-height: 35px;
        font-size: 16px;
        list-style-type: none;
        margin-left: 10px;
    }
    .billdata .rendertTil{
        color:#999;   
    }
    .billdata .renderShu{
        margin-left: 12px;
        display:inline-block;
        width: 60%;  
    }
    .applyBillConFoot{
        height: 7%;
        width: 100%;
        display: flex;
        justify-content: space-around;
    }
    .applyBillConFoot .el-button{
        width: 60%;
        height: 80%;
        font-size: 16px;
        margin-top: 2%;
    }
    .applyBillConMidTwo .vux-timeline-item-tail,
    .applyBillConMidTwo .vux-timeline-item-color{
        background-color: #4186E1;
    }
    .applyBillConMidTwo .vux-timeline-item-head-first{
        width: 25px;
        height: 25px;
        left: -7px;
        top: -4px;
    }
    .applyBillConMidTwo .vux-timeline-item-checked.weui-icon-success-no-circle::before{
        left: 6px;
        top: 5px;
    }
    .applyBillConMidTwo .vux-timeline-item-head{
        width: 18px;
        height: 18px;
        left: -3px;
        top: 2px;
    }
    .applyBillConMidTwo .vux-timeline-item-content{
        font-size: 16px;
        letter-spacing: 1px;
        color: #666;
    }
    .applyBillConMidTwo .vux-timeline-item-content h4{
        font-size: 20px;
        font-weight: 500;
    }
    .applyBillConMidTwo h4 .itemSpan{
        margin-right: 10px;
    }
</style>