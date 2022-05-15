<template>
  <div class="charts" ref="charts"></div>
</template>

<script>
import * as echarts from 'echarts';
export default {
  name: "",
  mounted() {
    let barCharts = echarts.init(this.$refs.charts)
    barCharts.setOption({
      xAxis:{
        show:false,
        type:"category"
      },
      yAxis:{
        show:false,
      },
      series:[{
        type:'bar',
        data:[10,4,78,24,8,36,99,66,77,55,44,22.15,19],
      }],
      grid:{
        left:0,
        right:0,
        top:0,
        bottom:0,
      }
  })
  },
};
</script>

<style scoped>
  .charts{
    width: 100%;
    height: 100%;
  }
</style>