<template>
  <div class="login">
    <div class="login-image">
      <Carousel :loop="setting.loop"
                :autoplay="setting.autoplay"
                :autoplay-speed="setting.autoplaySpeed"
                :dots="setting.dots"
                :radius-dot="setting.radiusDot"
                :trigger="setting.trigger"
                :arrow="setting.arrow">
        <CarouselItem>
          <div class="demo-carousel">
            <img src="@/assets/1.jpg" alt="">
          </div>
        </CarouselItem>
        <CarouselItem>
          <div class="demo-carousel">
            <img src="@/assets/2.jpg" alt="">
          </div>
        </CarouselItem>
        <CarouselItem>
          <div class="demo-carousel">
            <img src="@/assets/3.jpg" alt="">
          </div>
        </CarouselItem>
      </Carousel>
    </div>
    <div class="login-menu">
      <Menu name="auth" mode="horizontal" theme="light" class="login-menu-color" :active-name="activeName">
        <Menu mode="horizontal" theme="light" :active-name="activeName"
              class="login-menu-context login-menu-color">
          <MenuItem name="1" :to="{name: 'index'}">
            首页
          </MenuItem>
          <div class="float-right">
            <MenuItem name="2" :to="{name: 'login'}">
              请登录
            </MenuItem>
            <MenuItem name="3" :to="{name: 'register'}">
              注册账户
            </MenuItem>
            <MenuItem name="3-1">
              找回密码
            </MenuItem>
          </div>
        </Menu>
      </Menu>
    </div>

    <div class="login-text">
      <router-view name="auth"></router-view>
    </div>
  </div>
</template>

<script>
  import {
    Card,
    Menu,
    MenuItem,
    Carousel,
    CarouselItem,
    Icon,
    Form,
    FormItem,
    Button,
    Input,
    Divider,
    Avatar,
    Checkbox
  } from 'iview'
  import '@/assets/Icon-tencent/iconfont.css'
  import HeaderIndex from '@/components/auth/HeaderIndex'

  export default {
    name: 'index',
    data () {
      return {
        activeName: '',
        show: true,
        loginLock: false,
        setting: {
          loop: true,
          autoplay: true,
          autoplaySpeed: 10000,
          dots: 'none',
          radiusDot: false,
          trigger: 'click',
          arrow: 'never'
        }
      }
    },
    computed: {
      getMenuActives: function () {
        return this.$store.getters['index/getMenuActive']
      }
    },
    watch: {
      getMenuActives: function (a, b) {
        console.log('aaaa', a, b)
        this.activeName = a
      }
    },
    methods: {
      onLogin: function () {
        this.loginLock = true
      }
    },
    components: {
      Card,
      Menu,
      MenuItem,
      Carousel,
      CarouselItem,
      Icon,
      Form,
      FormItem,
      Button,
      Input,
      Divider,
      Avatar,
      Checkbox,

      'header-index': HeaderIndex
    }
  }
</script>

<style scoped>

  .login {
    width: 100vw;
    height: 100vh;
    position: relative;
  }

  .login-image {
    width: 100%;
    height: 100%;
    overflow: hidden;
  }

  .login-image img {
    width: 100%;
  }

  /* 顶部导航 */
  .login-menu {
    top: 0;
    width: 100vw;
    position: absolute;
    /*display: none;*/
  }

  .login-menu-context {
    width: 1200px;
    margin: auto;
  }

  /* 固定窗口大小 登录或者注册小窗口 */
  >>> .login-text .login-card .ivu-card {
    width: 25vw;
    margin: auto;
  }
</style>
