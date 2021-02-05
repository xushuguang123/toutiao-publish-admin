<template>
  <div>
    <el-card class="filter-card">
      <div slot="header" class="clearfix">
        <!-- 面包屑路径导航 -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
          <el-breadcrumb-item>内容管理</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- /面包屑路径导航 -->
      </div>
      <div class="text item">
        <!-- 数据筛选表单 -->
        <el-form ref="form" size="mini" :model="form" label-width="40px">
          <el-form-item label="状态">
            <el-radio-group v-model="form.resource">
              <el-radio label="全部"></el-radio>
              <el-radio label="草稿"></el-radio>
              <el-radio label="待审核"></el-radio>
              <el-radio label="审核通过"></el-radio>
              <el-radio label="审核失败"></el-radio>
              <el-radio label="已删除"></el-radio>
            </el-radio-group>
          </el-form-item>
          <el-form-item label="频道">
            <el-select v-model="form.region" placeholder="请选择">
              <el-option label="开发者资讯" value="shanghai"></el-option>
              <el-option label="区域二" value="beijing"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="日期">
            <el-date-picker
              v-model="form.date1"
              type="datetimerange"
              range-separator="至"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
            ></el-date-picker>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="onSubmit">筛选</el-button>
          </el-form-item>
        </el-form>
        <!-- /数据筛选表单 -->
      </div>
    </el-card>

    <el-card class="box-card">
      <div slot="header" class="clearfix">根据筛选条件共查询到 46197 条结果：</div>
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
          <el-table-column prop="date" label="封面"></el-table-column>
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
            <template>
              <el-button size="mini" circle icon="el-icon-edit" type="primary"></el-button>
              <el-button size="mini" circle type="danger" icon="el-icon-delete"></el-button>
            </template>
          </el-table-column>
        </el-table>
        <!-- /数据列表 -->
        <!-- 列表分页 -->
        <el-pagination class="list-card" layout="prev, pager, next" background :total="1000"></el-pagination>
        <!-- /列表分页 -->
      </div>
    </el-card>
  </div>
</template>

<script>
import { getArticles } from '@/api/article'
export default {
  name: 'ArticleIndex',
  components: {},
  props: {},
  data () {
    return {
      form: {
        name: '',
        region: '',
        date1: '',
        date2: '',
        delivery: false,
        type: [],
        resource: '',
        desc: ''
      },
      tableData: [{
        date: '2016-05-02',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1518 弄'
      }, {
        date: '2016-05-04',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1517 弄'
      }, {
        date: '2016-05-01',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1519 弄'
      }, {
        date: '2016-05-03',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      }],
      articles: [],
      articleStatus: [
        { status: 0, text: '草稿', type: 'info' },
        { status: 1, text: '待审核', type: '' },
        { status: 2, text: '审核通过', type: 'success' },
        { status: 3, text: '审核失败', type: 'warning' },
        { status: 4, text: '已删除', type: 'warning' }
      ]
    }
  },
  computed: {},
  watch: {},
  created () {
    this.loadArticles()
  },
  mounted () { },
  methods: {
    loadArticles () {
      getArticles().then(res => {
        console.log(res)
        this.articles = res.data.data.results
      })
    },
    onSubmit () {
      console.log('submit!')
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
