<template>
  <el-container class="layout-container">
    <el-aside class="aside" width="auto">
      <app-aside :is-collapse="isCollapse" />
    </el-aside>
    <el-container>
      <el-header class="header">
        <div>
          <i
            :class="{'el-icon-s-fold':isCollapse,'el-icon-s-unfold':!isCollapse}"
            @click="isCollapse = !isCollapse"
          ></i>
          <span>长沙贝肯软件开发有限公司</span>
        </div>
        <el-dropdown>
          <div class="avatar-warp">
            <img class="avatar" :src="user.photo" alt />
            <span>
              {{user.name}}
              <i class="el-icon-arrow-down el-icon--right"></i>
            </span>
          </div>
          <!-- <span>
            下拉菜单
            <i class="el-icon-arrow-down el-icon--right"></i>
          </span>-->
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item>设置</el-dropdown-item>
            <!-- 组件默认不识别原生事件,除非内部做了处理 -->
            <el-dropdown-item @click.native="onLogout">退出</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </el-header>
      <el-main class="main">
        <!-- 子路由出口 -->
        <router-view />
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
import AppAside from './components/aside'
import { getUserProfile } from '@/api/user'
export default {
  name: 'LayoutIndex',
  components: {
    AppAside
  },
  props: {},
  data () {
    return {
      user: '',
      isCollapse: false
    }
  },
  computed: {},
  watch: {},
  created () {
    // 组件初始化好, 请求获取用户资料
    this.loadUserProfile()
  },
  mounted () { },
  methods: {
    // 除了登录接口,其他接口都需要提供身份令牌,获取数据
    loadUserProfile () {
      getUserProfile().then(res => {
        // console.log(res)
        this.user = res.data.data
        // console.log(this.user)
      })
    },
    onLogout () {
      this.$confirm('确认退出吗? ', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 清除用户的登录状态
        window.localStorage.removeItem('user')

        // 跳转到登录页面
        this.$router.push('./login')
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取退出'
        })
      })
    }
  }
}
</script>
<style scoped lang=less>
.layout-container {
  position: fixed;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
}
.aside {
  background-color: #002033;
}
.header {
  height: 60px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #ccc;
}
.avatar-warp {
  display: flex;
  align-items: center;
  .avatar {
    width: 40px;
    height: 40px;
    border-radius: 50%;
    margin-right: 10px;
  }
}
.main {
  background-color: #e9eef3;
}
</style>
