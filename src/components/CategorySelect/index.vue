<template>
  <el-form :inline="true" class="demo-form-inline">
    <el-form-item label="一级筛选">
      <el-select
        placeholder="请选择"
        v-model="cFrom.category1Id"
        @change="handler1"
        :disabled="cateShow"
      >
        <el-option
          :label="c1.name"
          :value="c1.id"
          v-for="c1 in list1"
          :key="c1.id"
        ></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="二级筛选">
      <el-select
        placeholder="请选择"
        v-model="cFrom.category2Id"
        @change="handler2"
        :disabled="cateShow"
      >
        <el-option
          :label="c2.name"
          :value="c2.id"
          v-for="c2 in list2"
          :key="c2.id"
        ></el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="三级筛选">
      <el-select
        placeholder="请选择"
        v-model="cFrom.category3Id"
        @change="handler3"
        :disabled="cateShow"
      >
        <el-option
          :label="c3.name"
          :value="c3.id"
          v-for="c3 in list3"
          :key="c3.id"
        ></el-option>
      </el-select>
    </el-form-item>
  </el-form>
</template>

<script>
export default {
  name: "CategorySelect",
  props:['cateShow'],
  data() {
    return {
      list1: [],
      list2: [],
      list3: [],
      //收集对应的级别id
      cFrom: {
        category1Id: "",
        category2Id: "",
        category3Id: "",
      },
    };
  },
  mounted() {
    this.getCategory1List();
  },
  methods: {
    async getCategory1List() {
      let result = await this.$API.attr.reqCategory1List();
      if (result.code == 200) {
        this.list1 = result.data;
      }
    },
    async handler1() {
      this.list2 = [];
      this.list3 = [];
      this.cFrom.category2Id = "";
      this.cFrom.category3Id = "";
      const { category1Id } = this.cFrom;
      let result = await this.$API.attr.reqCategory2List(category1Id);
      this.list2 = result.data;
      this.$emit("getCategoryId", { categoryId: category1Id, level: 1 });
    },
    async handler2() {
      this.list3 = [];
      this.cFrom.category3Id = "";
      const { category2Id } = this.cFrom;
      let result = await this.$API.attr.reqCategory3List(category2Id);
      this.list3 = result.data;
      this.$emit("getCategoryId", { categoryId: category2Id, level: 2 });
    },
    handler3() {
      this.$emit("getCategoryId", { categoryId: this.cFrom.category3Id, level: 3 });
    },
  },
};
</script>

<style>
</style>