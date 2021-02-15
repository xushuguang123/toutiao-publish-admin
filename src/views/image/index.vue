<template>
  <div>
    <el-card>
      <div slot="header">
        <!-- 面包屑路径导航 -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item to="/">首页</el-breadcrumb-item>
          <el-breadcrumb-item>素材管理</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- /面包屑路径导航 -->
      </div>
      <div style="padding-bottom: 20px;">
        <el-radio-group v-model="radio1" size="mini">
          <el-radio-button label="全部"></el-radio-button>
          <el-radio-button label="收藏"></el-radio-button>
        </el-radio-group>
      </div>
      <!-- 素材列表 -->
    <el-row :gutter="10">
        <el-col :xs="12" :sm="6" :md="6" :lg="4">
            <el-image
            style="height: 100px"
            src="http://toutiao-img.itheima.net/FnDcV-AKqRsEH-zBVsXn-SHIzQlO"
            fit="cover"></el-image>
        </el-col>
        <el-col :xs="12" :sm="6" :md="6" :lg="4">
            <el-image
            style="height: 100px"
            src="http://toutiao-img.itheima.net/FnDcV-AKqRsEH-zBVsXn-SHIzQlO"
            fit="cover"></el-image>
        </el-col>
        <el-col :xs="12" :sm="6" :md="6" :lg="4">
            <el-image
            style="height: 100px"
            src="http://toutiao-img.itheima.net/FnDcV-AKqRsEH-zBVsXn-SHIzQlO"
            fit="cover"></el-image>
        </el-col>
        <el-col :xs="12" :sm="6" :md="6" :lg="4">
            <el-image
            style="height: 100px"
            src="http://toutiao-img.itheima.net/FnDcV-AKqRsEH-zBVsXn-SHIzQlO"
            fit="cover"></el-image>
        </el-col>
        <el-col :xs="12" :sm="6" :md="6" :lg="4">
            <el-image
            style="height: 100px"
            src="http://toutiao-img.itheima.net/FnDcV-AKqRsEH-zBVsXn-SHIzQlO"
            fit="cover"></el-image>
        </el-col>
        <el-col :xs="12" :sm="6" :md="6" :lg="4">
            <el-image
            style="height: 100px"
            src="http://toutiao-img.itheima.net/FnDcV-AKqRsEH-zBVsXn-SHIzQlO"
            fit="cover"></el-image>
        </el-col>
        <el-col :xs="12" :sm="6" :md="6" :lg="4">
            <el-image
            style="height: 100px"
            src="http://toutiao-img.itheima.net/FnDcV-AKqRsEH-zBVsXn-SHIzQlO"
            fit="cover"></el-image>
        </el-col>
        <el-col :xs="12" :sm="6" :md="6" :lg="4">
            <el-image
            style="height: 100px"
            src="http://toutiao-img.itheima.net/FnDcV-AKqRsEH-zBVsXn-SHIzQlO"
            fit="cover"></el-image>
        </el-col>
        <el-col :xs="12" :sm="6" :md="6" :lg="4">
            <el-image
            style="height: 100px"
            src="http://toutiao-img.itheima.net/FnDcV-AKqRsEH-zBVsXn-SHIzQlO"
            fit="cover"></el-image>
        </el-col>
    </el-row>
      <!-- /素材列表 -->
    </el-card>
  </div>
</template>

<script>
export default {
  name: 'ImageIndex',
  components: {},
  props: {},
  data () {
    return {
      radio1: '全部'
    }
  },
  computed: {},
  watch: {},
  created () {},
  mounted () {},
  methods: {}
}
</script>
<style scoped lang=less>
</style>
