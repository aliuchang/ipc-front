<template>
  <div class="card card-default">
    <!--头信息：改为选择块-->
    <div class="card-header">
      <ul class="nav nav-pills col-md-9">
        <h3 class="card-title">{{ ChartTitle }}</h3>
      </ul>
    </div>
    <!--头信息结束-->
    <div class="card-body">
      <div class="tab-content">
        <div class="tab-pane active" id="tab_1">
          <!--搜索项选择器-->
          <section class="content">
            <div class="container-fluid">
              <div class="relationship-graph col-md-12">
                <div ref="chartDom" :style="ChartStyle"></div>
                <!-- <div id="trend-graph" style="width:100%;height:400px;align-content:center;"></div> -->
              </div>
            </div>
            <!-- /.row -->
          </section>
          <!--搜索器选择项结束-->
        </div>
      </div>
      <!-- /.tab-content -->
    </div>
    <br />
    <br />
  </div>
</template>

<script>
import echarts from "echarts";
import { addListener, removeListener } from "resize-detector";
import debounce from "lodash/debounce";
export default {
  // ["BarTitle", "BarStyle", "option"]
  data() {
    return {
      chart: null
    };
  },
  props: {
    ChartTitle: {
      type: String
    },
    ChartStyle: {
      type: String
    },
    option: {
      type: Object,
      default: () => {}
    },
    link: {
      type: String
    }
  },
  watch: {
    option: {
      handler(val) {
        console.log(val);
        this.chart.setOption(val);
      },
      deep: true
    }
  },
  created() {
    this.resize = debounce(this.resize, 300);
  },
  mounted() {
    this.renderChart();
    addListener(this.$refs.chartDom, this.resize);
  },
  beforeDestroy() {
    removeListener(this.$refs.chartDom, this.resize);
    this.chart.dispose();
    this.chart = null;
  },
  methods: {
    resize() {
      this.chart.resize();
    },
    renderChart() {
      this.chart = echarts.init(this.$refs.chartDom);
      console.log("this.link", this.link);
      if (this.link) {
        let l = this.link;
        let op = this.option;
        let chartname = this.ChartTitle;
        this.chart.on("click", function(params) {
          console.log("Chart Params:", params);
          let name = params.seriesName;
          console.log("renderChart name:", name);
          if (chartname === "团队关系") {
            window.open(l + params.data.id, "_blank");
            return;
          }else {
            op.series.map(x => {
              if (x.name == name) {
                window.open(l + x.uuid, "_blank");
                return;
              }
            });
          }
          
        });
      }
      this.chart.setOption(this.option, true);
    }
  }
};
</script>

<style>
</style>
