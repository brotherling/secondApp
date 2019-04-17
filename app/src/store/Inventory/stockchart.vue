<template>
<div class="div1">
      <v-header :header="parentMsg"></v-header>
      <section>
          <div class="showhtml" id="Chart"></div>
      </section>
    <stockfoot :pcId="pcId" :planId="planId" :toggleIndex="1"></stockfoot>
</div>
</template>

 <script>
 import stockfoot from "../../components/stockfoot.vue"
   // 引入 ECharts 主模块
 let echarts = require('echarts/lib/echarts');
// 引入饼图
require('echarts/lib/chart/pie');
// 引入柱状图
require('echarts/lib/chart/bar');
require("echarts/lib/chart/line");
// 引入提示框和标题组件
require('echarts/lib/component/tooltip');
require('echarts/lib/component/title');
require('echarts/lib/component/legend');
export default {
  name: "saocode",
components:{stockfoot},
  data() {
    return {
      parentMsg: this.$route.query.name,
      pcId:this.$route.query.pcid,
      planId:this.$route.query.planId
    };
  },
  methods: {
    stockchart(Data, dispose) {
      let myChart = echarts.init(document.getElementById("Chart"));
      let option = {
            tooltip : {
        trigger: 'item',
        formatter: "{a} <br/>{b} : {c} ({d}%)"
    },
    legend: {
        orient: 'vertical',
        left: 'left',
        data:  ['盘亏','已盘','盘溢','盘盈']
    },
    series : [
        {
            name: '访问来源',
            type: 'pie',
            radius : '55%',
            center: ['50%', '60%'],
            data:Data.data,
            label: {
              normal: {
                // position: "inner",
                textStyle: {
                  fontWeight: "normal",
                  fontSize: 16
                }
              }
            },
             label: {
                normal: {
                    formatter: '{b|{b}：}{c}',
                    borderColor: '#aaa',
                    borderRadius: 4,
                    rich: {
                        a: {
                            color: '#999',
                            lineHeight: 22,
                            align: 'center'
                        },
                        hr: {
                            borderColor: '#aaa',
                            width: '100%',
                            borderWidth: 0.5,
                            height: 0
                        },
                        b: {
                            fontSize: 16,
                            lineHeight: 33
                        },
                        per: {
                            color: '#eee',
                            backgroundColor: '#334455',
                            padding: [2, 4],
                            borderRadius: 2
                        }
                    }
                }
            },
        }
    ]
      }
      // 使用刚指定的配置项和数据显示图表。
      myChart.setOption(option);
    },
  },
  mounted() {
      let Data = [];
      this.$http
        .get("/API/stockservice/GetStockTj",{params:{"pcId":this.pcId}}, {emulateJSON: true })
        .then(res => {
          Data = JSON.parse(res.data);
          this.stockchart(Data);
        });
  }
};
</script>
<style>
.showhtml {
  width: 100%;
  height: 500px;
}
</style>
