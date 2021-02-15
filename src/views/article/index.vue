<template>
  <div>
    <el-card class="filter-card">
      <div slot="header" class="clearfix">
        <!-- 面包屑路径导航 -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item to="/">首页</el-breadcrumb-item>
          <el-breadcrumb-item>内容管理</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- /面包屑路径导航 -->
      </div>
      <div class="text item">
        <!-- 数据筛选表单 -->
        <el-form size="mini" label-width="40px">
          <el-form-item label="状态">
            <el-radio-group v-model="status">
              <el-radio :label="null">全部</el-radio>
              <el-radio :label="0">草稿</el-radio>
              <el-radio :label="1">待审核</el-radio>
              <el-radio :label="2">审核通过</el-radio>
              <el-radio :label="3">审核失败</el-radio>
              <el-radio :label="4">已删除</el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="频道">
            <el-select v-model="channelId" placeholder="请选择频道">
              <el-option label="全部" :value="null"></el-option>
              <el-option :label="channel.name" :value="channel.id" v-for="(channel, index) in channels" :key="index"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="日期">
            <el-date-picker
              v-model="rangeDate"
              type="datetimerange"
              range-separator="至"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
              format="yyyy-MM-dd"
              value-format="yyyy-MM-dd"
            ></el-date-picker>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" :disabled="loading" @click="loadArticles(1)">筛选</el-button>
          </el-form-item>
        </el-form>
        <!-- /数据筛选表单 -->
      </div>
    </el-card>

    <el-card class="box-card">
      <div slot="header" class="clearfix">根据筛选条件共查询到 {{totalCount}} 条结果：</div>
      <div class="text item">
        <!-- 数据列表 -->
        <!--
          Table 表格组件
          1.把需要展示的数组列表数据绑定给 table 组件的 data 属性
          注意: 你不用去 v-for 遍历,他自己会遍历
          2.设计表格列 el-table-column
            width 可以设定表格列的宽度
            label 可以设定列的标题
            prop  用来设定要渲染的列表项数据字段,只能展示文本
          3.表格列默认只能渲染普通文本,如果需要展示其他内容,例如放个按钮啊,图片啊,那就需要自定义表格列模板：https://element.eleme.cn/#/zh-CN/component/table#zi-ding-yi-lie-mo-ban
        -->
        <el-table stripe size="mini" :data="articles" style="width: 100%">
          <el-table-column prop="date" label="封面">
            <template slot-scope="scope">
              <el-image style="width: 100px; height: 100px;" :src="scope.row.cover.images[0]" fit="cover" lazy>
                <div slot="error">
                  <img style="width: 100%" src="./pic_bg.png" alt="">
                </div>
              </el-image>
            </template>
          </el-table-column>
          <el-table-column prop="title" label="标题"></el-table-column>
          <el-table-column label="状态">
            <!-- 如果需要在自定义模板中获取当前遍历项数据,那么就在template 上声明 slot-scope="scope" -->
            <template slot-scope="scope">
              <!-- 优化文章状态展示处理 -->
              <el-tag
                :type="articleStatus[scope.row.status].type"
              >{{articleStatus[scope.row.status].text}}</el-tag>
              <!-- /优化文章状态展示处理 -->
              <!-- <el-tag v-if="scope.row.status === 0" type="warning">草稿</el-tag>
              <el-tag v-else-if="scope.row.status === 1">待审核</el-tag>
              <el-tag v-else-if="scope.row.status === 2" type="success">审核通过</el-tag>
              <el-tag v-else-if="scope.row.status === 3" type="danger">审核失败</el-tag>
              <el-tag v-else-if="scope.row.status === 4" type="info">已删除</el-tag>-->
            </template>
          </el-table-column>
          <el-table-column prop="pubdate" label="发布时间"></el-table-column>
          <el-table-column prop="address" label="操作">
            <!-- 如果需要自定义表格列模板,则把需要自定义的内容放到 template 里面 -->
            <template slot-scope="scope">
              <el-button
              size="mini"
              circle icon="el-icon-edit"
              type="primary"
              @click="$router.push('/publish?id=' + scope.row.id)"></el-button>
              <el-button
              size="mini"
              circle
              type="danger"
              icon="el-icon-delete"
              :disabled="loading"
              @click="onDeleteArticle(scope.row.id)"></el-button>
            </template>
          </el-table-column>
        </el-table>
        <!-- /数据列表 -->
        <!-- 列表分页 -->
        <!-- 分页组件中设定的每页数据大小应该和我们请求数据的每页大小保持一致,或者页码的计算就会出现问题 -->
        <el-pagination
        class="list-card"
        layout="prev, pager, next"
        background
        :total="totalCount"
        :page-size="pageSize"
        :disabled="loading"
        :current-page="page"
        @current-change="onCurrentChange"></el-pagination>
        <!-- /列表分页 -->
      </div>
    </el-card>
  </div>
</template>

<script>
import { getArticles, getArticleChannels, deleteArticle } from '@/api/article'
export default {
  name: 'ArticleIndex',
  components: {},
  props: {},
  data () {
    return {
      articles: [],
      articleStatus: [
        { status: 0, text: '草稿', type: 'info' },
        { status: 1, text: '待审核', type: '' },
        { status: 2, text: '审核通过', type: 'success' },
        { status: 3, text: '审核失败', type: 'warning' },
        { status: 4, text: '已删除', type: 'warning' }
      ],
      totalCount: 0, // 总数据条数
      pageSize: 10, // 每页大小
      status: null, // 查询文章的状态,不传就是全部
      channels: [], // 文章频道列表
      channelId: null, // 查询文章的频道
      rangeDate: null, // 筛选的范围日期
      loading: true, // 表单中加载中 loading
      page: 1 // 当前页码
    }
  },
  computed: {},
  watch: {},
  created () {
    this.loadChannels()
    this.loadArticles(1)
  },
  mounted () { },
  methods: {
    // 获取文章列表数据
    loadArticles (page = 1) {
      // 展示加载中的 loading
      this.loading = true
      getArticles({
        page,
        per_page: this.pageSize,
        status: this.status, // axios 不会发送数据为 null,undefined 之类的数据
        channel_id: this.channelId,
        begin_pubdate: this.rangeDate ? this.rangeDate[0] : null, // 开始日期
        end_pubdate: this.rangeDate ? this.rangeDate[1] : null // 截止日期
      }).then(res => {
        // console.log(res)
        const { results, total_count: totalCount } = res.data.data
        this.articles = results
        console.log(results)
        this.totalCount = totalCount
        // 关闭加载
        this.loading = false
      })
    },
    // 跳转页码
    onCurrentChange (page) {
      this.loadArticles(page)
    },
    // 获取文章分类频道
    loadChannels () {
      getArticleChannels().then(res => {
        this.channels = res.data.data.channels
      })
    },
    // 删除文章
    onDeleteArticle (articleId) {
      // console.log(articleId)
      // console.log(articleId.toString())
      this.$confirm('确认删除吗？', '删除提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 确认执行这里
        deleteArticle(articleId.toString()).then(res => {
          // 删除成功, 更新当前页的文章数据列表
          this.loadArticles(this.page)
          this.$message({
            message: '删除成功',
            type: 'success'
          })
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    }
  }
}
</script>
<style scoped lang="less">
.filter-card {
  margin-bottom: 30px;
}
.list-card {
  margin-top: 20px;
}
</style>
