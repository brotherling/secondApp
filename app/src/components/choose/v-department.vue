<template>
<div class="div1">
        <v-header :header="parentMsg"></v-header>
            <div style="border-bottom:1px solid #ccc;">
            <yd-search  :on-submit="getData"></yd-search>
            </div>
        <div class="department">
            <div class="DepartContent" v-for="(depart,index) in departData" :key="depart.levelCode">
                <i class="circle" @click="select(depart.deptId,depart.deptName,$event)"><span class=""></span></i>
                <div class="depAsset" @click="unfold(depart.deptId)">{{depart.deptName}}<x-icon v-if="depart.child.length!=0" style="position:absolute;right:10px;top:10px;" type="ios-arrow-down" size="20"></x-icon></div>
                <!-- 二级 -->
                <div :id="'dist'+depart.deptId" style="display:none;" v-if="depart.child.length!=0">
                    <div class="DepartContent distance" v-for="(depart1,index) in depart.child" :key="depart1.levelCode">
                        <i class="circle"  @click="select(depart1.deptId,depart1.deptName,$event)"><span class=""></span></i>
                        <div class="depAsset"  @click="unfold(depart1.deptId)">{{depart1.deptName}}<x-icon v-if="depart1.child.length!=0" style="position:absolute;right:10px;top:10px;" type="ios-arrow-down" size="20"></x-icon></div>
                        <!-- 三级 -->
                         <div :id="'dist'+depart1.deptId" style="display:none;" v-if="depart1.child.length!=0">
                            <div class="DepartContent distance" v-for="(depart2,index) in depart1.child" :key="depart2.levelCode">
                                <i class="circle"  @click="select(depart2.deptId,depart2.deptName,$event)"><span class=""></span></i>
                                <div class="depAsset"  @click="unfold(depart2.deptId)">{{depart2.deptName}}<x-icon v-if="depart2.child.length!=0" style="position:absolute;right:10px;top:10px;" type="ios-arrow-down" size="20"></x-icon></div>
                                <!-- 四级 -->
                                <div :id="'dist'+depart2.deptId" style="display:none;" v-if="depart2.child.length!=0">
                                    <div class="DepartContent distance" v-for="(depart3,index) in depart2.child" :key="depart3.levelCode">
                                        <i class="circle"  @click="select(depart3.deptId,depart3.deptName,$event)"><span class=""></span></i>
                                        <div class="depAsset"  @click="unfold(depart3.deptId)">{{depart3.deptName}}<x-icon v-if="depart3.child.length!=0" style="position:absolute;right:10px;top:10px;" type="ios-arrow-down" size="20"></x-icon></div>
                                        <!-- 五级 -->
                                        <div :id="'dist'+depart3.deptId" style="display:none;" v-if="depart3.child.length!=0">
                                            <div class="DepartContent distance" v-for="(depart4,index) in depart3.child" :key="depart4.levelCode">
                                                <i class="circle"  @click="select(depart4.deptId,depart4.deptName,$event)"><span class=""></span></i>
                                                <div class="depAsset"  @click="unfold(depart4.deptId)">{{depart4.deptName}}<x-icon v-if="depart4.child.length!=0" style="position:absolute;right:10px;top:10px;" type="ios-arrow-down" size="20"></x-icon></div>
                                                <!-- 无级 -->
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

            </div>
        </div>
</div>

</template>
<script>
    export default{
        name:"department",
        data(){
            return{
                parentMsg:"部门选择页面",
                searchdata:"",
                departData:"",
                id:"",
                name:""
            }
        },
        beforeRouteLeave(to, from, next) {
            // 设置下一个路由的 meta
           from.query.useDeptName = this.name;
           from.query.useDeptId = this.id;
           next();
          },
        methods: {
            select(i,e,event){
                let cycle=$(".circle");
                for(let j=0;j<cycle.length;j++){
                    $(".circle").eq(j).find("span").removeClass("click")
                }
                this.id=i;
                this.name=e;
                let dom=event.target;
                $(dom).find("span").addClass("click")
                $(".restoring").trigger("click");
            },
            unfold(i){
                $("#dist"+i).toggle();
            },
            getData(value){
                if(value==undefined){
                    value="";
                }
                this.$http.get("/API/baseservice/GetDeptByName", {params:{deptName:value}},{ emulateJSON: true })
                .then(res => {
                    this.departData=res.data;
                }
                )
            },
        },
        created(){
            this.getData()
        }
    }
</script>
<style>
.department{
  height:80%;
    width:100%;
    margin-top:10px;
    background:white;
    overflow: hidden;
    overflow-y:auto;
}
.DepartContent{
    /* height: 50px; */
    line-height:50px;
    background:white;
    /* position: relative; */
    border-bottom:1px solid #eee;/*no*/
}
.circle{
    width:25px;
    height:25px;
    border:1px solid #ccc;/*no*/
    border-radius: 50%;
    /* position: absolute;
    top:12px;
    left: 15px; */
}
.depAsset{
    display:inline-block;
    font-size:16px;
    margin-left:60px;
    width:100%;
}
.distance{
    margin-left:25px;
}
.click{
    height:15px;
    width:15px;
    border:1px solid #4B96D8;/*no*/
    border-radius:50%;
    background:#4B96D8;
    position:absolute;
    top:4px;
    left:4px;
}
</style>
