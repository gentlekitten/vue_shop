<template>
  <div>
    <!-- 面包屑区域 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>商品列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 卡片视图区域 -->
    <el-card>
      <el-row :gutter="20">
        <el-col :span="8">
          <el-input placeholder="请输入内容" v-model="queryInfo.query" clearable @clear="getGoodsList">
              <el-button slot="append" icon="el-icon-search" @click="getGoodsList"></el-button>
            </el-input>
        </el-col>
        <el-col :span="4">
          <el-button type="primary" @click="goAddpage">添加商品</el-button>
        </el-col>
      </el-row>
      <!-- tab表格区域 -->
      <el-table :data="goodslist" border stripe>
        <!-- 索引 -->
        <el-table-column type="index"></el-table-column>
        <el-table-column label="商品名称" prop="goods_name"></el-table-column>
        <el-table-column label="商品价格(元)" prop="goods_price" width="110px"></el-table-column>
        <el-table-column label="商品重量" prop="goods_weight" width="80px"></el-table-column>
        <el-table-column label="创建时间" prop="add_time" width="180px">
          <template slot-scope="scope">
            {{scope.row.add_time | dataForm}}
          </template>
        </el-table-column>
        <el-table-column label="操作" width="250px">
          <template slot-scope="scope">
            <el-button icon="el-icon-edit" type="primary" @click="editById(scope.row.goods_id)">编辑</el-button>
            <el-button icon="el-icon-delete" type="danger" @click="removeById(scope.row.goods_id)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页区域 -->
       <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="queryInfo.pagenum"
            :page-sizes="[5, 10, 15, 20]"
            :page-size="queryInfo.pagesize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="total" background>
          </el-pagination>
    </el-card>
    <!-- 修改分类区域对话框 -->
    <el-dialog
      title="修改商品"
      :visible.sync="editgoodsVisible"
      width="50%" @close="editgoodsClosed">
      <!-- 内容 -->
      <el-form :model="editgoods" :rules="editgoodsRules" ref="editgoodsRef" label-width="100px">
        <el-form-item label="商品名称:" prop="goods_name">
          <el-input v-model="editgoods.goods_name"></el-input>
        </el-form-item>
        <el-form-item label="价格:" prop="goods_price">
          <el-input v-model="editgoods.goods_price"></el-input>
        </el-form-item>
        <el-form-item label="数量:" prop="goods_number">
          <el-input v-model="editgoods.goods_number"></el-input>
        </el-form-item>
        <el-form-item label="重量:" prop="goods_weight">
          <el-input v-model="editgoods.goods_weight"></el-input>
        </el-form-item>
      </el-form>
     <!-- 底部区域 -->
     <span slot="footer" class="dialog-footer">
        <el-button @click="editgoodsVisible = false">取 消</el-button>
        <el-button type="primary" @click="editgoodsInfo">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 查询参数
      queryInfo: {
        query: '',
        // 当前页数
        pagenum: 1,
        // 每页几条
        pagesize: 10
      },
      // 商品列表
      goodslist: [],
      // 总数据条数
      total: 0,
      // 控制修改对话框的显示
      editgoodsVisible: false,
      editgoods: {},
      editgoodsRules: {
        goods_name: [
          { required: true, message: '请输入商品名称', trigger: 'blur' }],
        goods_price: [
          { required: true, message: '请输入商品价格', trigger: 'blur' }],
        goods_number: [
          { required: true, message: '请输入商品数量', trigger: 'blur' }],
        goods_weight: [
          { required: true, message: '请输入商品重量', trigger: 'blur' }]
      }
    }
  },
  created() {
    this.getGoodsList()
  },
  methods: {
    // 根据分页获取对应的商品列表
    async getGoodsList() {
      const { data: res } = await this.$http.get('goods',
        { params: this.queryInfo })
      if (res.meta.status !== 200) {
        return this.$message.error('获取商品列表失败')
      }
      // this.$message.success('获取商品列表成功')
      console.log(res.data)
      this.goodslist = res.data.goods
      this.total = res.data.total
    },
    handleSizeChange(newSize) {
      this.queryInfo.pagesize = newSize
      this.getGoodsList()
    },
    handleCurrentChange(newNum) {
      this.queryInfo.pagenum = newNum
      this.getGoodsList()
    },
    // 根据id删除参数
    async removeById(id) {
      const confirmResult = await this.$confirm('此操作将永久删除该参数, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(err => err)
      // 如果参数确认删除，则返回值为字符串 confirm
      // 如果参数取消删除，则返回值为字符串 cancel
      if (confirmResult !== 'confirm') {
        return this.$message.info('已取消删除')
      }
      const { data: res } = await this.$http.delete(`goods/${id}`)
      if (res.meta.status !== 200) return this.$message.error('删除参数失败！')
      this.$message.success('删除参数成功！')
      this.getGoodsList()()
    },
    goAddpage() {
      this.$router.push('/goods/add')
    },
    // 监听修改关闭事件
    editgoodsClosed() {
      this.$refs.editgoodsRef.resetFields()
    },
    async editById(id) {
      const { data: res } = await this.$http.get('goods/' + id)
      if (res.meta.status !== 200) {
        return this.$message.error('查询商品信息失败！')
      }
      // 把获取到的商品信息对象，保存到 编辑表单数据对象中
      this.editgoods = res.data
      console.log(this.editgoods)
      this.editgoodsVisible = true
    },
    // 监听修改商品提交
    editgoodsInfo() {
      this.$refs.editgoodsRef.validate(async valid => {
        if (!valid) return
        // 发起修改的请求
        // console.log(this.editgoods)
        const { data: res } = await this.$http.put('goods/' + this.editgoods.goods_id, {
          goods_name: this.editgoods.goods_name,
          goods_price: this.editgoods.goods_price,
          goods_number: this.editgoods.goods_number,
          goods_weight: this.editgoods.goods_weight
        })
        if (res.meta.status !== 201) {
          return this.$message.error('编辑商品信息失败！')
        }
        this.$message.success('编辑商品信息成功！')
        this.getGoodsList()
        this.editgoodsVisible = false
      })
    }
  }
}

</script>

<style lang="less" scoped>
</style>
