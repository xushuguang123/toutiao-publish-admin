<template>
  <div class="comment-container">
    <el-card>
      <div slot="header">
        <!-- 面包屑路径导航 -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item to="/">首页</el-breadcrumb-item>
          <el-breadcrumb-item>评论管理</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- /面包屑路径导航 -->
      </div>
      <el-table :data="articles" style="width: 100%">
        <el-table-column prop="title" label="标题" width="180">
        </el-table-column>
        <el-table-column
          prop="total_comment_count"
          label="总评论数"
          width="180"
        >
        </el-table-column>
        <el-table-column prop="fans_comment_count" label="粉丝评论数">
        </el-table-column>
        <el-table-column prop="comment_status" label="评论状态">
          <template slot-scope="scope">
            {{ scope.row.comment_status ? '正常' : '关闭' }}
          </template>
        </el-table-column>
        <el-table-column prop="comment_status" label="操作">
          <template slot-scope="scope">
            <el-switch
              v-model="scope.row.comment_status"
              active-color="#13ce66"
              inactive-color="#ff4949"
              :disabled="scope.row.statusDisabled"
              @change="onStatusChange(scope.row)"
            >
            </el-switch>
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page.sync="page"
        :page-sizes="[10, 20, 30, 50]"
        :page-size.sync="pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="totalCount"
        background
      >
      </el-pagination>
    </el-card>
  </div>
</template>

<script>
import { getArticles, updateCommentStatus } from '@/api/article'
export default {
  name: 'CommentIndex',
  components: {},
  props: {},
  data () {
    return {
      articles: [{
        title: '',
        total_comment_count: 0,
        fans_comment_count: 0,
        comment_status: true

      }], // 文章评论管理数据
      currentPage4: 1,
      totalCount: 0, // 总数据条数
      pageSize: 10, // 每页大小
      page: 1 // 当前激活的页码
    }
  },
  computed: {},
  watch: {},
  created () {
    // 获取评论列表数据列表
    this.loadArticles()
  },
  mounted () { },
  methods: {
    // 获取评论列表数据
    loadArticles (page = 1) {
      // 获取评论列表接口
      getArticles({
        response_type: 'comment',
        page,
        per_page: this.pageSize
      }).then(res => {
        // console.log(res)
        const { results } = res.data.data
        results.forEach(article => {
          article.statusDisabled = false
        })
        this.articles = results
        this.totalCount = res.data.data.total_count
      })
    },
    onStatusChange (article) {
      // 禁用开关
      article.statusDisabled = true
      // 请求提交修改文章评论状态
      console.log(article)
      updateCommentStatus(
        article.id.toString(), article.comment_status
      ).then(res => {
        // 启用开关
        article.statusDisabled = false
        this.$message({
          type: 'success',
          message: article.comment_status ? '开启文章评论状态' : '关闭文章评论状态'
        })
        // console.log(res)
      })
    },
    handleSizeChange () {
      this.loadArticles(1)
    },
    // 点击页码跳转的页码数
    handleCurrentChange (page) {
      // console.log(page)
      this.loadArticles(page)
    }
  }
}
</script>
<style scoped lang=less>
</style>
