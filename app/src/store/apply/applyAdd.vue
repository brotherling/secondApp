<template>
    <div class="applyAdd">
        <v-header :header="header"></v-header>
        <div class="applyAddContent">
            <div class="applyAddConTop">
                <el-form label-position="left" label-width="80px" :model="formSel">
                    <el-form-item label="领用人">
                        <el-input v-model="formSel.str30Name" @focus="toPer" suffix-icon="el-icon-arrow-right" placeholder="请选择" v-bind:value="formSel.str30Name"></el-input>
                    </el-form-item>
                    <el-form-item label="领用部门">
                        <el-input v-model="formSel.str180Name" @focus="toDept" suffix-icon="el-icon-arrow-right" placeholder="请选择"></el-input>
                    </el-form-item>
                     <el-form-item label="领用日期">
                        <el-input v-model="formSel.ASM_B2_40" @focus='dateTime()' v-if="timeFlag"></el-input>
                    </el-form-item>
                    <el-form-item label="存放地点">
                        <el-input v-if="addressFlag" v-model="formSel.ASM_B2_100" @focus="toDes" suffix-icon="el-icon-arrow-right" placeholder="请选择"></el-input>
                        <el-input v-else v-model="formSel.ASM_B2_100"></el-input>
                    </el-form-item>
                    <el-form-item label="资产用途">
                        <input type="text" v-model="formSel.ASM_B2_90" @focus="write()" class="assetuse">
                        <!-- <el-input v-model="formSel.ASM_B2_90" @focus="write()"></el-input> -->
                    </el-form-item>
                    <el-form-item label="备注">
                        <!-- <input type="textarea" v-model="formSel.ASM_B2_230" @focus="write()"> -->
                        <textarea v-model="formSel.ASM_B2_230" @focus="write()" class="assetmemo"></textarea>
                        <!-- <el-input type="textarea" v-model="formSel.ASM_B2_230" @focus="write()"></el-input> -->
                    </el-form-item>
                </el-form>
                <div class="applyFile">
                    <div class="applyFileLeft"  @click="openfile()">
                        <span>附件</span>
                        <input type="file" @change="changeFile($event)" style="display: none" ref="file" multiple="multiple">   
                    </div>
                    <div class="applyFileRight" v-for="showfile in filedataName" :key="showfile.fileName">
                        {{showfile.fileName}}
                        <span @click="clickdel(showfile.fileName,showfile.fileId)">
                            <icon type="clear"></icon>
                        </span>
                    </div>
                </div>
            </div>
            <div class="applyAddConMid">
                <div class="middleHeader">
                    <div class="headerLeft">条码编号</div>
                    <div class="headerRight">
                        <el-input v-model="searchVal" placeholder="请输入内容"></el-input>
                        <el-button type="primary"  @click="applySearch()">搜索</el-button>
                    </div>
                </div>
                <div class="middleContent">
                    <div class="applydata" v-for="(item, index) in applychooseData" :key="item.ASM_A1_10">
                        <p class="applydataHead" @click="applydel(index)">
                            <span class="detail">资产明细({{index+1}})</span>
                            <span class="applydel">删除</span>
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
            </div>
        </div>
        <div class="addApply">
            <el-button icon="el-icon-plus" @click="toChoose">添加资产明细</el-button>
        </div>
        <div class="applyAddFoot" v-if="footFlag">
            <el-button type="primary" @click="applySave()">保存</el-button>
        </div>
        <div class="applyaddfoot" v-else>
            <el-button type="primary" @click="applySave()">提交</el-button>
        </div>
        <v-floatball @btnSao="applySao"></v-floatball>
    </div>

</template>
<script>
export default {
    name: 'applyAdd',
    data(){
        return {
            header:'领用登记',
            addressFlag: true, //改变地址输入形式
            footFlag: true,
            fileList: [],
            filedataName: [], //上传文件名
            FileData:[],
            filedata:[],
            formSel: {
                str30Name: '',
                ASM_B2_30: '',
                ASM_B2_180: '',
                str180Name: '',
                ASM_B2_40: '',
                ASM_B2_100: '',
                ASM_B2_140: '',
                ASM_B2_90: '',
                ASM_B2_230:'',
            },    //人员部门选择
            searchVal:'',
            count: 1,
            operatetype: this.$route.query.operatetype,
            menuId:this.$route.query.id,
            applychooseId: [],
            applychooseData: [],
            timeFlag: true,
            textFlag: true,
            editId: this.$route.query.editId,
            editFile: [],   //获取修改功能传过来的文件名
        }
    },
    computed:{
        ...Vuex.mapState([
            "applyadd",
            "applychoose",
            "applychooseIds",
            'applyfile',
            'applyfilename'
        ]),
    },
    created() {
        //控制存放地点是选择还是输入
        this.$http.get("/API/baseservice/GetAddressType")
        .then(res => {
            if(res.body != 1){
                this.addressFlag = false
            }
        });
    },
    methods: {
        ...Vuex.mapActions({
            getPerDate: 'setApplyDate',
            getChooseDate: 'setApplyChooseDate',
            getChooseIds: "setApplyChooseIds",
            getApplyFile: 'setApplyFile',
            getApplyFileName: 'setApplyFileName'
        }),
        setApplyAdd(e){
            this.getPerDate(e)
        },
        setApplyChoose(e){
            this.getChooseDate(e)
        },
        setApplyCsIds(e){
            this.getChooseIds(e)
        },
        setApplyDataFile(e){
            this.getApplyFile(e)
        },
        setApplyDataFileName(e){
            this.getApplyFileName(e)
        },
        //选择人员
        toPer(){
            this.$router.push({ path:'/personnel' })
        },
        //选择部门
        toDept(){
            this.$router.push({ path:'/department' })
        },
        //选择地址
        toDes(){
            this.$router.push({ path:'/designated' })
        },
        toChoose(){
            this.$router.push({ path:'/applyChoose',query: {operatetype:this.operatetype,id:this.menuId,applycsId:this.applychooseId,chooseData:this.applychooseData} })
        },
        //仿ios选择时间
        dateTime(){
            var that = this
            this.$vux.datetime.show({
                cancelText: '取消',
                confirmText: '确定',
                format: 'YYYY-MM-DD',
                value: '',
                onConfirm (val) {
                    that.formSel.ASM_B2_40 = val
                    //时间渲染不上去,利用刷新dom来渲染
                    that.timeFlag = false
                    that.$nextTick(() => {
                        that.timeFlag = true
                    })
                },
                onHide () {
                    const _this = this
                },
                onShow () {
                    const _this = this
                }
            })
        },
        write(){
            this.setApplyAdd(this.formSel)
        },
        //删除资产明细
        applydel(index){
            this.applychooseData.splice(index,1)
            this.applychooseId.splice(index,1)
            this.footFlag = false
        },
        loadData() {
            this.$http
                .post(
                "/API/apply/GetChooseAssetList",
                {
                    menuId: this.menuId,
                    operatetype: this.operatetype,
                    pageNumber: '1',
                    pageSize: "10",
                    sortName: "ASM_A1_20",
                    order: "asc",
                    url: this.url,
                    param: this.searchVal,
                    ids : [],
                },
                { emulateJSON: true }
                )
                .then(res => {
                    let data = JSON.parse(res.data);
                    if(data.total != 0){
                        this.applychooseId.push(data.rows[0]['ASM_A1_10'])
                        this.applychooseData.push(data.rows[0])   
                    }else{
                        this.$dialog.alert({mes:'请输入正确编号'});
                    }
                });
        },
        //搜索
        applySearch(e){
            if(e!=undefined){
                this.searchVal=e
            }
            this.loadData();
        },
        //扫码
        applySao(){
             this.saoma(this.applySearch)
        },
        //选择文件
        openfile() {
            this.$refs.file.click();
        },
        //选择文件
        changeFile(event){
            var obj = event.target;
            var fl = obj.files[0];
            if (fl != undefined) {
                this.fileList.push(fl)
                this.applyFile(this.fileList)
            }
        },
        //删除文件
        clickdel(name, id) {
            if(this.fileList.length !=0 && this.editFile.length == 0){
                for (var i = 0, filelength = this.fileList.length; i < filelength; i++) {
                    if (this.fileList[i].name == name) {
                        this.fileList.splice(i, 1);
                        this.applyFile(this.fileList)
                        return;
                    }
                 }
            }else{
                this.$http.post("/API/baseservice/DeleteFile",{"":{id:id}},{ emulateJSON: true })
                .then(res => {
                    if(res.status == 200){
                        for (var i = 0, filelength = this.filedataName.length; i < filelength; i++) {
                            if (this.filedataName[i].fileName == name) {
                                this.filedataName.splice(i, 1);
                                this.editFile.splice(i, 1);
                                return;
                            }
                        }
                    }      
                })
            }
                
        },
        applyFile(value){
            this.filedataName=[];
            this.filedata=[];
            this.FileData=value;
            let that=this;
            this.FileData.forEach(function (e, i) {
                 var dui={};
                 dui.fileName = e.name;
                 dui.fileId="";
                that.filedataName.push(dui)
                var reader = new FileReader();//这是核心,读取操作就是由它完成.
                   reader.readAsDataURL(e);
                   reader.onload = function () {
                        var data = reader.result.replace("data:" + e.type + ";base64,", '');
                        that.filedata.push(data)
                    }
            })
            this.setApplyDataFile(this.filedata)
            this.setApplyDataFileName(this.filedataName)
        },
        //保存文件
        submitfile(e){
            if(this.filedata.length == 0){
                this.filedata = ''
            }
            this.$http.post("/API/baseservice/SaveFile",{"":{attachType:'006006',filedata: this.filedata,filedataName:this.filedataName,id:e}},{ emulateJSON: true })
            .then(res => {
                //   console.log(res)          
            })
        },
        //保存数据
        applySave(){
            // var oldForm =JSON.stringify(this.formSel).replace(/useUserId/g, "ASM_B2_30").replace(/useUserName/g, "str30Name")
            // .replace(/useDeptId/g, "ASM_B2_180").replace(/useDeptName/g, "str180Name").replace(/insDt/g, "ASM_B2_40")
            // .replace(/addressName/g, "ASM_B2_100").replace(/adressId/g, "ASM_B2_140").replace(/assetPurpose/g, "ASM_B2_90").replace(/memo/g, "ASM_B2_230");
            // var newForm = JSON.parse(oldForm);
            this.$http.post("/API/apply/Save",{"":{apply:this.formSel,assetId:this.applychooseId,opType:0}},{ emulateJSON: true })
                .then(res => {
                    this.submitfile(res.data.resultNum)
                    this.$router.go(-1)
                })
        },
    },
    mounted() {
      
    },
    beforeRouteEnter (to, from, next) {
            next(vm => {
               if(from.name=="personnel"){
                   //获取已选取的文件
                   vm.filedata = vm.$store.state.applyfile
                    vm.filedataName = vm.$store.state.applyfilename
                   //获取先选取的资产和id
                   vm.applychooseData = vm.$store.state.applychoose
                   vm.applychooseId = vm.$store.state.applychooseIds
                   vm.formSel = vm.$store.state.applyadd
                   vm.formSel.ASM_B2_180=from.query.data.userDeptId
                   vm.formSel.str180Name=from.query.data.userDeptName
                   vm.formSel.ASM_B2_30=from.query.data.userId
                   vm.formSel.str30Name=from.query.data.userName
                   vm.setApplyAdd(vm.formSel)
                }else if(from.name=="department"){
                    //获取已选取的文件
                   vm.filedata = vm.$store.state.applyfile
                    vm.filedataName = vm.$store.state.applyfilename
                    //获取先选取的资产和id
                    vm.applychooseData = vm.$store.state.applychoose
                    vm.applychooseId = vm.$store.state.applychooseIds
                   vm.formSel = vm.$store.state.applyadd
                   vm.formSel.ASM_B2_180=from.query.useDeptId
                   vm.formSel.str180Name=from.query.useDeptName
                   vm.setApplyAdd(vm.formSel)
                }else if(from.name=="designated"){
                    //获取已选取的文件
                   vm.filedata = vm.$store.state.applyfile
                    vm.filedataName = vm.$store.state.applyfilename
                    //获取先选取的资产和id
                    vm.applychooseData = vm.$store.state.applychoose
                    vm.applychooseId = vm.$store.state.applychooseIds
                    vm.formSel = vm.$store.state.applyadd
                   vm.formSel.ASM_B2_140=from.query.adressId
                   vm.formSel.ASM_B2_100=from.query.addressName
                   vm.setApplyAdd(vm.formSel);
                }else if(from.name=="applyChoose"){
                    //获取已选取的文件
                   vm.filedata = vm.$store.state.applyfile
                    vm.filedataName = vm.$store.state.applyfilename
                    //获取先前选的人员信息
                   vm.formSel = vm.$store.state.applyadd
                   //判断是通过确认返回还是通过返回键返回
                   if(from.query.ids.length > 0){
                         vm.applychooseId = from.query.ids
                        vm.applychooseData = from.query.csData
                   }else{
                        vm.applychooseData = vm.$store.state.applychoose
                        vm.applychooseId = vm.$store.state.applychooseIds
                   }
                   //二维数组转一维
                //    vm.applychooseData = [].concat.apply([], chooseArr);
                   vm.setApplyChoose(vm.applychooseData)
                   vm.setApplyCsIds(vm.applychooseId)
                }else if(from.name == 'homemenu'){
                    //清空之前存在vuex的内容
                    vm.setApplyAdd(vm.formSel)
                    vm.setApplyChoose(vm.applychooseData)
                    vm.setApplyCsIds(vm.applychooseId)
                    vm.setApplyDataFile(vm.filedata)
                    vm.setApplyDataFileName(vm.filedataName)
                }else if(from.name == 'applyList'){
                    //清空之前存在vuex的内容
                    vm.setApplyAdd(vm.formSel)
                    vm.setApplyChoose(vm.applychooseData)
                    vm.setApplyCsIds(vm.applychooseId)
                    vm.setApplyDataFile(vm.filedata)
                    vm.setApplyDataFileName(vm.filedataName)
                    vm.footFlag = false
                     //获取要编辑的数据
                    vm.$http
                    .get(
                    "API/apply/Edit",
                    {params:{id:vm.editId}},{ emulateJSON: true })
                    .then(res => {
                        console.log(res)
                        vm.formSel = res.body.apply
                        vm.applychooseData = res.body.list
                        vm.filedataName = res.body.File
                        vm.editFile = res.body.File   //用于判断文件怎么删除
                        res.body.list.forEach(function(item) {
                            vm.applychooseId.push(item.ASM_A1_10);
                        }, vm);
                        //存储修改数据到vuex
                        vm.setApplyAdd(vm.formSel)
                        vm.setApplyChoose(vm.applychooseData)
                        vm.setApplyCsIds(vm.applychooseId)
                        vm.setApplyDataFileName(vm.filedataName)
                    });
                }
            })
        },
}
</script>
<style>
    .applyAdd{
        height: 100%;
    }
    .applyAddContent{
        height: 75%;
        width: 100%;
    }
    .applyAddConTop{
        width: 90%;
        height: 56%;
        overflow-y: auto;
        margin: 0 5%;
        padding: 2% 0;
        border-bottom: 1px solid #666;
    }
    .applyAddConTop .assetuse{
        width: 100%;
        height: 35px;
        padding-left: 15px;
        border-radius: 4px;
        color: #606266;
        border: 1px solid #ccc;
        background-color: #fff;
    }
    .applyAddConTop .assetmemo{
        width: 100%;
        height: 50px;
        padding-left: 15px;
        padding-top: 10px;
        border-radius: 4px;
        color: #606266;
        border: 1px solid #ccc;
        background-color: #fff;
    }
    .applyAddConTop .el-form-item__content{
        line-height: 0;
    }
    .applyAddConTop .el-form-item{
        margin-bottom: 1%
    }
    .applyAddConTop .el-input__inner{
        height: 35px;
    }
    .applyAddConTop .el-textarea__inner{
        height: 50px;
    }
    .applyAddConTop .applyFile{
        width: 100%;
        height: 40px;
    }
    .applyAddConTop .applyFileLeft{
        width: 28%;
        float: left;
        height: 40px;
        line-height: 40px;
    }
    .applyAddConTop .applyFileRight{
        width: 72%;
        float: right;
    }
    .applyAddConTop .applyFileRight span{
        display: inline-block;
        height: 10px;
        width: 10px;
    }
    .applyAddConMid{
        width: 90%;
        height: 43%;
        margin: 0 5%;
        padding: 2% 0;
    }
    .middleHeader{
        width: 100%;
        height: 45px;
    }
    .middleHeader .headerLeft{
        width: 30%;
        height: 45px;
        color: #606266;
        float: left;
        line-height: 45px;
    }
    .middleHeader .headerRight{
        width: 70%;
        height: 40px;
        float: left;
    }
    .middleHeader .headerRight .el-input{
        width: 70%
    }
    .middleHeader .headerRight .el-button{
        height: 40px;
    }
    .middleContent{
        height: 86%;
        overflow-y: auto;
    }
    .middleContent .applydata{
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
    .applydataHead{
        width: 100%;
        height: 40px;
        line-height: 40px;
        border: 1px solid #666;
        background-color: #DCDFE6;
        padding: 0 20px 0 8px;
        margin-bottom: 5px;
    }
    .applydataHead .detail{
        float: left;
    }
    .applydataHead .applydel{
        float: right;
    }
    .applydata li{
        line-height: 35px;
        font-size: 16px;
        list-style-type: none;
        margin-left: 10px;
    }
    .applydata .rendertTil{
        color:#999;   
    }
    .applydata .renderShu{
        margin-left: 12px;
        display:inline-block;
        width: 60%;  
    }
    .addApply{
        height: 6%;
        width: 100%;
        text-align: center;
    }
    .addApply .el-button{
        width: 80%;
        height: 80%;
        font-size: 16px;
    }
    .addApply .el-icon-plus{
        color: blue;
    }
    .applyAddFoot{
        height: 6%;
        width: 100%;
        display: flex;
        justify-content: space-around;
    }
    .applyAddFoot .el-button{
        width: 40%;
        height: 80%;
        font-size: 16px;
    }
    .applyaddfoot{
        height: 6%;
        width: 100%;
        display: flex;
        justify-content: space-around;
    }
    .applyaddfoot .el-button{
        width: 60%;
        height: 80%;
        font-size: 16px;
    }
</style>