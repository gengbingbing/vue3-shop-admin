<template>
  <div class="layout">
    <el-container v-if="state.showMenu" class="container">
      <el-aside class="aside">
        <div class="head">
          <div>
            <span>Vue3 商城 Admin</span>
          </div>
        </div>
        <div class="line" />
        <el-menu
          :default-openeds="state.defaultOpen"
          background-color="#222832"
          text-color="#fff"
          :router="true"
          :default-active="state.currentPath"
        >
          <el-menu-item index="/SystemIntroduce"
            ><i class="el-icon-data-line" />系统简介</el-menu-item
          >
          <el-menu-item index="/dashboard"
            ><i class="el-icon-odometer" />销售统计</el-menu-item
          >
          <el-submenu index="1">
            <template #title>
              <span><i class="el-icon-s-home" />首页配置</span>
            </template>
            <el-menu-item-group>
              <el-menu-item index="/home/swiper"
                ><i class="el-icon-picture" />轮播图配置</el-menu-item
              >
              <el-menu-item index="/home/hot"
                ><i class="el-icon-star-on" />热销商品配置</el-menu-item
              >
              <el-menu-item index="/home/new"
                ><i class="el-icon-sell" />新品上线配置</el-menu-item
              >
              <el-menu-item index="/home/recommend"
                ><i class="el-icon-thumb" />为你推荐配置</el-menu-item
              >
            </el-menu-item-group>
          </el-submenu>
          <el-menu-item index="/category"
            ><i class="el-icon-menu" />分类管理</el-menu-item
          >
          <el-menu-item index="/good"
            ><i class="el-icon-s-goods" />商品管理</el-menu-item
          >
          <el-menu-item index="/guest"
            ><i class="el-icon-user-solid" />会员管理</el-menu-item
          >
          <el-menu-item index="/order"
            ><i class="el-icon-s-order" />订单管理</el-menu-item
          >

          <el-submenu index="4">
            <template #title>
              <span><i class="el-icon-setting" />系统管理</span>
            </template>
            <el-menu-item-group>
              <el-menu-item index="/account"
                ><i class="el-icon-lock" />修改密码</el-menu-item
              >
            </el-menu-item-group>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-container class="content">
        <Header />
        <div class="main">
          <router-view />
        </div>
        <Footer />
      </el-container>
    </el-container>
    <el-container v-else class="container">
      <router-view />
    </el-container>
  </div>
</template>

<script>
import { onUnmounted, reactive } from 'vue';
import Header from '@/components/Header.vue';
import Footer from '@/components/Footer.vue';
import { useRouter } from 'vue-router';
import { pathMap, localGet } from '@/utils';
export default {
  name: 'App',
  components: {
    Header,
    Footer,
  },
  setup() {
    const noMenu = ['/login'];
    const router = useRouter();
    const state = reactive({
      defaultOpen: ['/SystemIntroduce'],
      showMenu: true,
      currentPath: '/SystemIntroduce',
      count: {
        number: 1,
      },
    });
    // 监听浏览器原生回退事件
    if (window.history && window.history.pushState) {
      history.pushState(null, null, document.URL);
      window.addEventListener(
        'popstate',
        () => {
          if (!localGet('token')) {
            state.showMenu = false;
          }
        },
        false
      );
    }
    const unwatch = router.beforeEach((to, from, next) => {
      if (to.path == '/login') {
        // 如果路径是 /login 则正常执行
        next();
      } else {
        // 如果不是 /login，判断是否有 token
        if (!localGet('token')) {
          // 如果没有，则跳至登录页面
          next({ path: '/login' });
        } else {
          // 否则继续执行
          next();
        }
      }
      state.showMenu = !noMenu.includes(to.path);
      state.currentPath = to.path;
      document.title = pathMap[to.name];
    });
    onUnmounted(() => {
      unwatch();
    });
    return {
      state,
    };
  },
};
</script>

<style scoped>
.layout {
  min-height: 100vh;
  background-color: #ffffff;
}
.container {
  height: 100vh;
}
.aside {
  width: 230px !important;
  background-color: #222832;
  overflow: hidden;
  overflow-y: auto;
  -ms-overflow-style: none;
  overflow: -moz-scrollbars-none;
}
.aside::-webkit-scrollbar {
  display: none;
}
.head {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 50px;
}
.head > div {
  display: flex;
  align-items: center;
}
.head img {
  width: 50px;
  height: 50px;
  margin-right: 10px;
}
.head span {
  font-size: 20px;
  color: #ffffff;
}
.line {
  border-top: 1px solid hsla(0, 0%, 100%, 0.05);
  border-bottom: 1px solid rgba(0, 0, 0, 0.2);
}
.content {
  display: flex;
  flex-direction: column;
  max-height: 100vh;
  overflow: hidden;
}
.main {
  height: calc(100vh - 100px);
  overflow: auto;
  padding: 10px;
}
</style>
<style>
body {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
.el-menu {
  border-right: none !important;
}
.el-submenu {
  border-top: 1px solid hsla(0, 0%, 100%, 0.05);
  border-bottom: 1px solid rgba(0, 0, 0, 0.2);
}
.el-submenu:first-child {
  border-top: none;
}
.el-submenu [class^='el-icon-'] {
  vertical-align: -1px !important;
}
a {
  color: #409eff;
  text-decoration: none;
}
.el-pagination {
  text-align: center;
  margin-top: 20px;
}
.el-popper__arrow {
  display: none;
}
</style>