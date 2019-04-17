<template>
    <div class="foot">
        <ul>
            <li @click="Homepage($event);home()"><span class="TopImg1"></span><br /><span>首页</span></li>
            <li @click="chart($event);state()"><span class="TopImg2"></span><br /><span>图表</span></li>
            <li @click="Ewmcode"><span class="TopImg3" ></span><br /><span>扫码</span></li>
            <li @click="ME($event);person()"><span class="TopImg4"></span><br /><span>我的</span></li>
            <li @click="Message($event);mes()"><span class="TopImg5"></span><br /><span>账户</span></li>
        </ul>
    </div>
</template>
<script>
export default {
    name:'foot',
    data () {
        return {
            fontdata:["首页","图表","扫码","我的","账户"]
        }
    },
    methods:{
        restor(){
            let setli=$(".foot ul li");
            let num=0;
            for(let i=0;i<5;i++){
                 num=i+1;
                $(setli[i]).children("span").eq(0).removeClass("TopImg"+num+"t").addClass("TopImg"+num);
                 $(setli[i]).children("span").eq(1).removeClass("SizeColor");
            }
        },
        //foot小标高亮
        home(){
            this.restor();
            global.cliction=1;
            this.reload();
        },
        mes(){
            this.restor();
            global.cliction=5;
            this.reload();
        },
        state(){
            this.restor();
            global.cliction=2;
            this.reload();
        },
        person(){
            this.restor();
            global.cliction=4;
            this.reload();
        },
        //对应路由切换
        Homepage:function(e){
            this.$router.push({path:'/home'});
        },
        Message:function(e){
            this.$router.push({path:'/personalCenter'});
        },
        chart(e){
            this.$router.push({path:'/homemenu',query:{id:"000114",text:"资产报表"}})
        },
        ME(e){
            this.$router.push({
                path:"/assetpage/",
            query: { name:"个人资产查询", id:"22",url:"/asm/Asset/Search?tabid=22&assetstatus=manage&operatetype=1&APIUrl=assetpage"}

            });       
            
        },
        Adminste(e){
            this.restor();
            this.$router.push({
                path:"/assetpage/",
            query: { name:"管辖资产查询", saodata:e,id:"123",url:"/asm/Asset/Search?tabid=123&assetstatus=manage&operatetype=1&APIUrl=assetpage"}
            });      
            this.reload(); 
        },
        Ewmcode(){ 
        if(this.$route.path!="/assetpage/"){
            this.saoma(this.Adminste)
        }else{
            this.$emit('refreshbizlines')
        }
        },
        reload(){
        let setli=$(".foot ul li");
        switch(global.cliction){
            case 1:
            $(setli[0]).children("span").eq(0).removeClass("TopImg1").addClass("TopImg1t");
            $(setli[0]).children("span").eq(1).addClass("SizeColor");           
            break;
            case 2:
            $(setli[1]).children("span").eq(0).removeClass("TopImg2").addClass("TopImg2t");
            $(setli[1]).children("span").eq(1).addClass("SizeColor");           
            break;
            case 4:
            $(setli[3]).children("span").eq(0).removeClass("TopImg4").addClass("TopImg4t");
            $(setli[3]).children("span").eq(1).addClass("SizeColor");           
            break;
            case 5:
            $(setli[4]).children("span").eq(0).removeClass("TopImg5").addClass("TopImg5t");
            $(setli[4]).children("span").eq(1).addClass("SizeColor");           
            break;
        }
        }
    },
    mounted(){
         let jsURL=location.href.split('#')[0];
        this.reload();
    }
}
</script>
<style scoped>
.foot{
    height:10%;
    width:100%;  
    border-top: 1px solid #ccc;/*no*/
	position: fixed;
    bottom:0;
    background:#FBFCFF;
}
.foot ul{
    width:90%;
    height: 100%;
    margin-left:5%;
    display: flex;
    align-items: center;
    justify-content:space-around;
}
.foot ul li{
    height:100%;
    float: left;
    list-style: none;
    position:relative;
    color:#B4BABF; 
    text-align: center;
    top:15%;
}
.TopImg1{
    width:30px;
    height:30px;
    background: url("../assets/shouye.png") no-repeat;
    background-size:cover;
    display: inline-block;
}
.TopImg2{
    width:30px;
    height:30px;
    background: url("../assets/tubiao.png") no-repeat;
    background-size:cover;
    display: inline-block;
}
.TopImg3{
    width:30px;
    height:30px;
    background: url("../assets/shaoma.png") no-repeat;
    background-size:cover;
    display: inline-block;
}
.TopImg4{
    width:30px;
    height:30px;
    background: url("../assets/me.png") no-repeat;
    background-size:cover;
    display: inline-block;
}
.TopImg5{
    width:30px;
    height:30px;
    background: url("../assets/zhanghu.png") no-repeat;
    background-size:cover;
    display: inline-block;
}
.TopImg1t{
    width:30px;
    height:30px;
    background: url("../assets/Toggleqie.png") no-repeat;
    background-size:cover;
    display: inline-block;
}
.TopImg2t{
    width:30px;
    height:30px;
    background: url("../assets/Toggletubiao.png") no-repeat;
    background-size:cover;
    display: inline-block;
}
.TopImg4t{
    width:30px;
    height:30px;
    background: url("../assets/Toggleme.png") no-repeat;
    background-size:cover;
    display: inline-block;
}
.TopImg5t{
    width:30px;
    height:30px;
    background: url("../assets/Toggleuser.png") no-repeat;
    background-size:cover;
    display: inline-block;
}

</style>
