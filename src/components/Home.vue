<template>
  <el-container class="home-container">
    <!--头部区域-->
    <el-header>
      <div>
        <img height="40" src="../assets/logo.png" width="40">
        <span>后台管理系统</span>
      </div>
      <el-button type="info" @click="logout">退出登录</el-button>
    </el-header>
    <!--页面下半部分主体-->
    <el-container>
      <!--侧边栏-->
      <el-aside :width="isCollapse ? '64px':'200px'">
        <!--折叠条-->
        <div class="toggle-button" @click="toggleCollapse">|||</div>
        <!--侧边栏菜单-->
        <el-menu :collapse="isCollapse" :collapse-transition="false"
                 :default-active="activePath" :router="true"
                 :unique-opened="true" active-text-color="#409EFF"
                 background-color="#333744" text-color="#fff"
        >
          <!--一级菜单-->
          <el-submenu v-for="item in menuList" :key="item.id" :index="item.id+''">
            <!--一级菜单的模板区域-->
            <template slot="title">
              <!--图标-->
              <i :class="iconsObj[item.id]"></i>
              <!--文本-->
              <span>{{ item.authName }}</span>
            </template>
            <!--二级菜单-->
            <el-menu-item v-for="subItem in item.children"
                          :key="subItem.id"
                          :index="'/'+subItem.path"
                          @click="saveNavState('/'+subItem.path)"
            >
              <template slot="title">
                <i class="el-icon-menu"></i>
                <span>{{ subItem.authName }}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <!--右侧主体内容-->
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  name: 'Home',
  data () {
    return {
      //被激活的链接地址
      activePath: '',
      //是否折叠
      isCollapse: false,
      //左侧菜单数据
      menuList: [],
      iconsObj: {
        '125': 'iconfont icon-user',
        '103': 'iconfont icon-tijikongjian',
        '101': 'iconfont icon-shangpin',
        '102': 'iconfont icon-danju',
        '145': 'iconfont icon-baobiao'
      }
    }
  },
  created () {
    this.getMenuList()
    this.activePath = window.sessionStorage.getItem('activePath')
  },
  methods: {
    //保存链接的激活状态
    saveNavState (activePath) {
      window.sessionStorage.setItem('activePath', activePath)
      this.activePath = activePath
    },
    //菜单的折叠与展开
    toggleCollapse () {
      this.isCollapse = !this.isCollapse
    },
    //获取所有的菜单
    async getMenuList () {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) {
        return this.$message({
          message: res.meta.msg,
          type: 'error',
          duration: 900
        })
      }
      this.menuList = res.data
    },
    //退出登录
    logout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    }
  }
}

</script>

<style lang="less" scoped>
.toggle-button {
  background-color: #4A5064;
  font-size: 10px;
  line-height: 24px;
  color: #fff;
  text-align: center;
  letter-spacing: 0.2em;
  cursor: pointer;
}

.iconfont {
  margin-right: 10px;
}

.home-container {
  height: 100%;
}

.el-header {
  background-color: #373D41;
  display: flex;
  justify-content: space-between;
  padding-left: 0;
  align-items: center;
  color: #fff;
  font-size: 25px;

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
</style>
