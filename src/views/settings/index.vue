<template>
  <div class="setting-container">
    <el-card>
      <div slot="header">
        <!-- 面包屑路径导航 -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item to="/">首页</el-breadcrumb-item>
          <el-breadcrumb-item>个人设置</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- /面包屑路径导航 -->
      </div>
      <el-row>
        <el-col :span="14">
          <el-form ref="form" :model="user" label-width="80px">
            <el-form-item label="编号"> {{ user.id }} </el-form-item>
            <el-form-item label="手机"> {{ user.mobile }} </el-form-item>
            <el-form-item label="媒体名称">
              <el-input v-model="user.name"></el-input>
            </el-form-item>
            <el-form-item label="媒体介绍">
              <el-input v-model="user.intro" type="textarea"></el-input>
            </el-form-item>
            <el-form-item label="邮箱">
              <el-input v-model="user.email"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="onSubmit">保存</el-button>
            </el-form-item>
          </el-form>
        </el-col>
        <el-col :offset="2" :span="4">
          <label for="file">
            <el-avatar
              shape="square"
              :size="150"
              fit="cover"
              :src="user.photo"
            ></el-avatar>
            <p style="display: flex; justify-content: center; width: 150px">
              点击修改头像
            </p>
          </label>
          <input
            id="file"
            type="file"
            hidden
            ref="file"
            @change="onFileChange"
          />
        </el-col>
      </el-row>
    </el-card>
    <el-dialog title="修改头像" :visible.sync="dialogVisible" append-to-body>
      <img width="150" :src="previewImage" />
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogVisible = false"
          >确 定</el-button
        >
      </span>
    </el-dialog>
  </div>
</template>

<script>
import { getUserProfile } from '@/api/user.js'
export default {
  name: 'SettingsIndex',
  components: {},
  props: {},
  data () {
    return {
      user: {
        email: '',
        id: null,
        intro: '',
        mobile: '',
        name: '',
        photo: ''
      }, // 用户资料
      dialogVisible: false,
      previewImage: ''
    }
  },
  computed: {},
  watch: {},
  created () {
    this.loadUser()
  },
  mounted () { },
  methods: {
    onSubmit () { },
    // 获取用户信息
    loadUser () {
      getUserProfile().then(res => {
        this.user = res.data.data
        console.log(res)
      })
    },
    onFileChange () {
      // 展示弹出层, 预览用户选择的图片
      this.dialogVisible = true

      // 处于图片预览
      const file = this.$refs.file
      const blobData = window.URL.createObjectURL(file.files[0])
      this.previewImage = blobData

      // 解决选择相同文件不触发 change
      // console.log(this.$refs.file)
      this.$refs.file.value = ''
    }
  }
}
</script>
<style scoped lang=less>
</style>
