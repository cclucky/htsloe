<template>
  <div>
    <el-card style="margin: 20px 0px">
      <CategorySelect @getCategoryId="getCategoryId" :cateShow="!isAttrShow"></CategorySelect>
    </el-card>
    <el-card>
      <!-- 默认展示部分 -->
      <div v-show="isAttrShow">
        <el-button
          type="primary"
          icon="el-icon-plus"
          :disabled="!category3Id"
          @click="addAttr"
          >添加</el-button
        >
        <el-table border :data="attrList" style="width: 80%">
          <el-table-column align="center" type="index" label="序号" width="80">
          </el-table-column>
          <el-table-column prop="attrName" label="属性值名称" width="180">
          </el-table-column>
          <el-table-column prop="prop" label="属性值列表" width="width">
            <template v-slot:default="slotProps">
              <el-tag
                type="success"
                v-for="attrValue in slotProps.row.attrValueList"
                :key="attrValue.id"
                style="margin: 0 2px"
                >{{ attrValue.valueName }}</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column prop="name" label="操作" width="200">
            <template v-slot:default="slotProps">
              <el-button
                type="primary"
                icon="el-icon-edit"
                size="mini"
                @click="updateAttr(slotProps.row)"
                >修改</el-button
              >
              <el-button type="danger" icon="el-icon-delete" size="mini"
                >删除</el-button
              >
            </template>
          </el-table-column>
        </el-table>
      </div>
      <!-- 修改部分页面 -->
      <div v-show="!isAttrShow">
        <el-form ref="form" label-width="80px" :model="attrInfo">
          <el-form-item label="属性名">
            <el-input
              placeholder="请输入属性名"
              v-model="attrInfo.attrName"
              style="width: 200px"
            ></el-input>
          </el-form-item>
          <el-button
            type="primary"
            icon="el-icon-plus"
            style="margin 20px 0"
            @click="addAttrValue"
            :disabled="!attrInfo.attrName"
            >添加属性</el-button
          >
          <el-button @click="isAttrShow = true">取消</el-button>
          <el-table
            border
            style="width: 100%; margin: 20px 0"
            :data="attrInfo.attrValueList"
          >
            <el-table-column
              type="index"
              align="center"
              label="序号"
              width="80"
            >
            </el-table-column>
            <el-table-column prop="prop" label="属性名名称" width="width">
              <template v-slot:default="slotProps">
                <el-input
                  v-if="slotProps.row.flag"
                  v-model="slotProps.row.valueName"
                  placeholder="请输入内容"
                  size="mini"
                  @blur="toLook(slotProps.row)"
                  :ref="slotProps.$index"
                ></el-input>
                <span
                  v-else
                  @click="toEdit(slotProps.row, slotProps.$index)"
                  style="display: block"
                  >{{ slotProps.row.valueName }}</span
                >
              </template>
            </el-table-column>
            <el-table-column prop="prop" label="操作" width="200">
              <template v-slot:default="slotProps">
                <el-popconfirm :title="`确定删除${slotProps.row.valueName}吗？`" @onConfirm="deleteAttrValue(slotProps.$index)">
                  <el-button
                    type="danger"
                    icon="el-icon-delete"
                    size="mini"
                    slot="reference"
                  ></el-button>
                </el-popconfirm>
              </template>
            </el-table-column>
          </el-table>
        </el-form>
        <el-button type="primary" style="margin 20px 0" @click="addOrUpdateAttr" :disabled="attrInfo.attrValueList.length<1">保存</el-button>
        <el-button @click="isAttrShow = true">取消</el-button>
      </div>
    </el-card>
  </div>
</template>

<script>
import cloneDeep from "lodash/cloneDeep";
export default {
  data() {
    return {
      category1Id: "",
      category2Id: "",
      category3Id: "",
      attrList: [],
      isAttrShow: true,
      //新增或者修改属性的
      attrInfo: {
        attrName: "",
        attrValueList: [],
        categoryId: 0,
        categoryLevel: 3,
      },
    };
  },
  methods: {
    async getAttrList() {
      let { category1Id, category2Id, category3Id } = this;
      let result = await this.$API.attr.reqAttrList(
        category1Id,
        category2Id,
        category3Id
      );
      if (result.code == 200) {
        this.attrList = result.data;
      }
    },
    addAttr() {
      this.isAttrShow = false;
      this.attrInfo = {
        attrName: "",
        attrValueList: [],
        categoryId: this.category3Id,
        categoryLevel: 3,
      };
    },
    getCategoryId({ categoryId, level }) {
      if (level == 1) {
        this.category1Id = categoryId;
        this.category2Id = "";
        this.category3Id = "";
      } else if (level == 2) {
        this.category2Id = categoryId;
        this.category3Id = "";
      } else {
        this.category3Id = categoryId;
        this.getAttrList();
      }
    },
    addAttrValue() {
      this.attrInfo.attrValueList.push({
        attrId: this.attrInfo.id,
        valueName: "",
        flag: true,
      });
      this.$nextTick(() => {
        this.$refs[this.attrInfo.attrValueList.length - 1].focus();
      });
    },
    updateAttr(row) {
      this.isAttrShow = false;
      //cloneDeep深拷贝一下
      this.attrInfo = cloneDeep(row);
      this.attrInfo.attrValueList.forEach((item) => {
        this.$set(item, "flag", false);
      });
    },
    toLook(row) {
      if (row.valueName.trim() == "") {
        this.$message("不合法的输入内容");
        return;
      }
      let isRepat = this.attrInfo.attrValueList.some((item) => {
        if (row !== item) {
          return row.valueName == item.valueName;
        }
      });
      if (isRepat) return;
      row.flag = false;
    },
    toEdit(row, $index) {
      row.flag = true;
      this.$nextTick(() => {
        this.$refs[$index].focus();
      });
    },
    deleteAttrValue($index){
      this.attrInfo.attrValueList.splice($index,1)
    },
   async addOrUpdateAttr(){
      this.attrInfo.attrValueList =this.attrInfo.attrValueList.filter((item)=>{
        if(item.valueName!=''){
          delete item.flag
          return true
        }
      })
        try {
          await this.$API.attr.reqAddAttr(this.attrInfo);
          this.isAttrShow = true;
          this.$message({type:'success',message:'保存成功'});
          this.getAttrList()
        } catch (error) {
          this.$message({type:'success',message:'保存失败'})
        }
    }
  },
};
</script>

<style>
</style>