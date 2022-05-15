<template>
  <div>
    <el-card style="margin: 20px 0px">
      <CategorySelect
        @getCategoryId="getCategoryId"
        :cateShow="scene != 0"
      ></CategorySelect>
    </el-card>
    <el-card style="margin: 20px 0px">
      <div v-show="scene == 0">
        <el-button type="primary" style="margin: 10px 0" @click="addSpu"
          >添加spu</el-button
        >
        <el-table style="width: 100%" border :data="records">
          <el-table-column type="index" label="序号" width="80" align="center">
          </el-table-column>
          <el-table-column prop="spuName" label="spu名称" width="width">
          </el-table-column>
          <el-table-column prop="description" label="spu描述" width="width">
          </el-table-column>
          <el-table-column prop="prop" label="操作" width="width">
            <template v-slot:default="slotProps">
              <el-button
                type="success"
                icon="el-icon-plus"
                size="mini"
                title="添加sku"
                @click="addSku(slotProps.row)"
              ></el-button>
              <el-button
                type="warning"
                icon="el-icon-edit"
                size="mini"
                title="修改spu"
                @click="updateSpu(slotProps.row)"
              ></el-button>
              <el-button
                type="info"
                icon="el-icon-info"
                size="mini"
                title="查看当前spu全部sku列表"
                @click="handler(slotProps.row)"
              ></el-button>
              <el-popconfirm
                title="确定删除吗？"
                style="margin: 0 10px"
                @onConfirm="deleteSpu(slotProps.row)"
              >
                <el-button
                  type="danger"
                  icon="el-icon-delete"
                  size="mini"
                  title="删除spu"
                  slot="reference"
                ></el-button>
              </el-popconfirm>
            </template>
          </el-table-column>
        </el-table>
        <!--           @size-change="handleSizeChange"
          @current-change="handleCurrentChange" -->
        <el-pagination
          style="text-align: center"
          :current-page="page"
          :page-sizes="[3, 9, 15]"
          :page-size="limit"
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          layout="prev, pager, next, jumper,->,total, sizes"
          :total="total"
        >
        </el-pagination>
      </div>
      <SpuForm
        v-show="scene == 1"
        @changeScene="changeScene"
        ref="spu"
      ></SpuForm>
      <SkuForm
        v-show="scene == 2"
        ref="sku"
        @changeScenes="changeScenes"
      ></SkuForm>
      <el-dialog :title="spu.spuName" :visible.sync="dialogTableVisible" :before-close="close">
        <el-table :data="spuList" border v-loading="loading" >
            <el-table-column
              prop="skuName"
              label="名称"
              width="width" >
            </el-table-column>
             <el-table-column
              prop="price"
              label="价格"
              width="width" >
            </el-table-column>
             <el-table-column
              prop="weight"
              label="重量"
              width="width" >
            </el-table-column>
             <el-table-column
              label="默认图片"
              width="width" >
              <template v-slot:default="{row}">
                <img :src="row.skuDefaultImg" alt="" style="width:100px;weight:100px">
              </template>
            </el-table-column>
        </el-table>
      </el-dialog>
    </el-card>
  </div>
</template>

<script>
import SkuForm from "./SkuForm";
import SpuForm from "./SpuForm";
export default {
  name: "Spu",
  components: { SkuForm, SpuForm },
  data() {
    return {
      category1Id: "",
      category2Id: "",
      category3Id: "",
      attrList: [],
      page: 1,
      limit: 3,
      records: [],
      total: 0,
      scene: 0,
      dialogTableVisible: false,
      spu:{},
      spuList:[],
      loading: true
    };
  },
  methods: {
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
        this.getSpuList();
      }
    },
    handleCurrentChange(page) {
      this.page = page;
      this.getSpuList();
    },
    handleSizeChange(limit) {
      this.limit = limit;
      this.getSpuList();
    },
    async getSpuList() {
      const { page, limit, category3Id } = this;
      let result = await this.$API.spu.reqSpuLIst(page, limit, category3Id);
      if (result.code == 200) {
        this.records = result.data.records;
        this.total = result.data.total;
      }
    },
    addSpu(row) {
      this.scene = 1;
      this.$refs.spu.addOrUpdate(this.category3Id);
    },
    updateSpu(row) {
      this.scene = 1;
      this.$refs.spu.initSpuData(row);
    },
    changeScene({ scene, flag }) {
      this.scene = scene;
      if (flag == "修改") {
        this.getSpuList(this.page);
      } else {
        this.getSpuList((this.page = 1));
      }
    },
    async deleteSpu(row) {
      let resule = await this.$API.spu.reqDeleteSpu(row.id);
      if (resule.code == 200) {
        this.$message({ type: "success", message: "删除成功" });
        this.getSpuList(this.records.length > 1 ? this.page : this.page - 1);
      }
    },
    addSku(row) {
      this.scene = 2;
      this.$refs.sku.getData(this.category1Id, this.category2Id, row);
    },
    changeScenes(scene) {
      this.scene = scene;
    },
    async handler(spu){
      this.spu=spu;
     this.dialogTableVisible=true;
    let result =await this.$API.sku.reqSkuList(spu.id)
     if(result.code==200){
       this.spuList = result.data;
       this.loading=false;
     }
    },
    close(done){
      this.loading = true;
      this.spuList=[];
      done()
    }
  },
};
</script>

<style>
</style>