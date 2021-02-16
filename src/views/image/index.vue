<template>
  <div class="image-container">
    <el-card>
      <div slot="header">
        <!-- 面包屑路径导航 -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item to="/">首页</el-breadcrumb-item>
          <el-breadcrumb-item>素材管理</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- /面包屑路径导航 -->
      </div>
      <div class="action-head">
        <el-radio-group v-model="collect" size="mini" @change="onCollectChange">
          <el-radio-button :label="false">全部</el-radio-button>
          <el-radio-button :label="true">收藏</el-radio-button>
        </el-radio-group>
        <el-button size="mini" type="success" @click="dialogTableVisible = true"
          >上传素材</el-button
        >
      </div>
      <!-- 素材列表 -->
      <el-row :gutter="10">
        <el-col
          :xs="12"
          :sm="8"
          :md="6"
          v-for="(img, index) in images"
          :key="index"
        >
          <el-image
            style="width: 180px; height: 180px"
            :src="img.url"
            fit="cover"
          ></el-image>
        </el-col>
      </el-row>
      <!-- /素材列表 -->
    </el-card>
    <el-dialog
      title="上传素材"
      :visible.sync="dialogTableVisible"
      :append-to-body="true"
    >
      <!-- upload 组件本身就支持请求提交上传操作,说白了你使用它不用自己去发请求,它会自己发.
        请求方法: 默认就是POST
        请求路径: action 必须写完整路径
        请求体: name 字段名
     -->
      <el-upload
        drag
        action="http://api-toutiao-web.itheima.net/mp/v1_0/user/images"
        :headers="uploadHeaders"
        name="image"
        multiple
        :show-file-list="false"
        :on-success="onUploadSuccess"
        class="upload-center"
      >
        <i class="el-icon-upload"></i>
        <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
      </el-upload>
    </el-dialog>
    <!-- 列表分页 -->
    <!-- 分页组件中设定的每页数据大小应该和我们请求数据的每页大小保持一致,或者页码的计算就会出现问题 -->
    <el-pagination
      layout="prev, pager, next"
      background
      :total="totalCount"
      :page-size="pageSize"
      :current-page="page"
      @current-change="onCurrentChange"
    ></el-pagination>
    <!-- /列表分页 -->
  </div>
</template>

<script>
import { getImages } from '@/api/image'
export default {
  name: 'ImageIndex',
  components: {},
  props: {},
  data () {
    const user = JSON.parse(window.localStorage.getItem('user'))
    return {
      collect: false, // 默认查询全部素材列表
      images: [], // 图片素材列表
      dialogTableVisible: false,
      uploadHeaders: {
        Authorization: `Bearer ${user.token}`
      }, // 配置请求参数
      totalCount: 0, // 总数据条数
      pageSize: 12, // 每页大小
      page: 1 // 当前页码
    }
  },
  computed: {},
  watch: {},
  created () {
    this.loadImages()
  },
  mounted () { },
  methods: {
    loadImages (collect = false, page = 1) {
      getImages({
        collect,
        page,
        per_page: this.pageSize
      }).then(res => {
        this.images = res.data.data.results
        this.totalCount = res.data.data.total_count
        // console.log(res)
      })
    },
    // 监听 radio 的值是否发生变化
    onCollectChange (value) {
      // console.log(value)
      this.loadImages(value)
    },
    onUploadSuccess () {
      // 关闭对话框
      this.dialogTableVisible = false
      // 更新素材列表
      this.loadImages(this.collect)
    },
    // 跳转页码
    onCurrentChange (page) {
      this.loadImages(this.collect, page)
    }
  }
}
</script>
<style scoped lang=less>
.action-head {
  padding-bottom: 20px;
  display: flex;
  justify-content: space-between;
}
.upload-center {
  display: flex;
  justify-content: center;
}
</style>
