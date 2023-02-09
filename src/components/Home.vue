<template>

  <el-container class="home_container">
      <!-- 头部布局 -->
    <el-header>
    <div>
      <img src="../assets/heima.png" />
      <span>电商管理系统</span>
    </div>
    <el-button type="info" @click="logout">退出</el-button>
    </el-header>
    <el-container>
        <!-- 侧面布局 -->
      <el-aside :width="isCollapse ? '64px' : '200px'">
        <!-- 侧边菜单栏 -->
        <div class="toggle-button" @click="toggleCollapse">|||</div>
        <el-menu background-color="#333744" text-color="#fff" active-text-color="#409BFF" unique-opened :collapse="isCollapse"
:collapse-transition="false" router :default-active="activePath">
          <!-- 一级菜单栏 -->
              <el-submenu :index="item.id+''" v-for="item in menulist" :key="item.id">
                <!-- 模板 -->
                <template slot="title">
                  <!-- 图标 -->
                  <i :class="iconObj[item.id]"></i>
                  <!-- 文本 -->
                  <span>{{item.authName}}</span>
                </template>
                <!-- 二级菜单栏 -->
                <el-menu-item :index="'/' + butItem.path" v-for="butItem in item.children" :key="butItem.id"
@click="saveNavState('/' + butItem.path)">
                  <template slot="title">
                    <!-- 图标 -->
                    <i class="el-icon-menu"></i>
                    <!-- 文本 -->
                    <span>{{butItem.authName}}</span>
                  </template>
                </el-menu-item>
              </el-submenu>
            </el-menu>
      </el-aside>
        <!-- 主题布局 -->
      <el-main>
        <!-- 占位符 -->
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  data() {
    return {
      menulist: [],
      iconObj: {
        '125': 'iconfont icon-user',
        '103': 'iconfont icon-tijikongjian',
        '101': 'iconfont icon-shangpin',
        '102': 'iconfont icon-danju',
        '145': 'iconfont icon-baobiao'
      },
      isCollapse: false,
      // 被激活的链接地址
      activePath: ''
    }
  },
  created() {
    this.getMenuList()
    this.activePath = window.sessionStorage.getItem('activePath')
  },
  methods: {
    logout() {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    // 获取所有的菜单
    async getMenuList() {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      this.menulist = res.data
      console.log(res)
    },
    toggleCollapse() {
      this.isCollapse = !this.isCollapse
    },
    saveNavState(activePath) {
      window.sessionStorage.setItem('activePath', activePath)
      this.activePath = activePath
    }
  }
}
</script>

<style lang="less" scoped>
.home_container {
  height: 100%;
}
.el-header {
  background-color: #373d41;
  display: flex;
  justify-content: space-between;
  padding-left: 0;
  align-items: center;
  color: #fff;
  font-size: 20px;
  > div {
    display: flex;
    align-items: center;
    span {
      margin-left: 15px;
    }
  }
}
.el-aside {
  background-color: #333744;
  .el-menu {
    border-right: none;
  }
}
.el-main {
  background-color: #eaedf1;
}
.iconfont {
  margin-right: 10px;
}
.toggle-button {
  background-color: #4a5064;
  font-size: 10px;
  line-height: 24px;
  color: #fff;
  text-align: center;
  letter-spacing: 0.2em;
  cursor: pointer;
}
</style>
