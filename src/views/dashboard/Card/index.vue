<template>
  <div>
    <el-row :gutter="10">
      <el-col :span="6">
        <el-card>
          <Detail title="总销售额" currte="￥66666">
            <template v-slot:cardMian>
              <div style="line-height: 60px">周同比54.69%---日同比 18.88%</div>
            </template>
            <template v-slot:footer>
              <span>日销售额----￥11111 </span>
            </template>
          </Detail>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card>
          <Detail title="访问量" currte="23333">
            <template v-slot:cardMian>
              <lineChart></lineChart>
            </template>
            <template v-slot:footer>
              <span>日访问量1437 </span>
            </template>
          </Detail>
        </el-card>
      </el-col>
      <el-col :span="6">
        <el-card>
          <Detail title="交易成功数量" currte="7777">
            <template v-slot:cardMian> 
              <barChart></barChart>
               </template>
            <template v-slot:footer>
              <span>转化率 64% </span>
            </template>
          </Detail>
        </el-card>
      </el-col>
      <el-col :span="6"
        ><el-card>
          <Detail title="活动效果" currte="4396">
            <template v-slot:cardMian> 
              <progressCharts></progressCharts>
               </template>
            <template v-slot:footer>
              <span>周同比48.69%===日同比16.66%</span>
            </template></Detail
          >
        </el-card></el-col
      >
    </el-row>
  </div>
</template>

<script>
import Detail from "./Detail";
import lineChart from "./lineChart";
import barChart from "./barChart";
import progressCharts from "./progressCharts";
export default {
  name: "Card",
  components: { Detail, lineChart ,barChart,progressCharts},
};
</script>

<style>
</style>