<!-- 注册账户 -->
<template>
  <div class="login-card">
    <div class="login-card-center">
      <h1>未来办公网</h1>
      <small>未来办公网 —— 致力打造高效智慧办公一体化生态圈</small>
    </div>
    <Card>
      <div slot="title" class="login-title">
        <div class="login-card-left">
          <h5 class="login-card-title">注册商城账户</h5>
          <p class="login-card-text">您注册的账户，请妥善保管</p>
        </div>
        <div class="login-card-right">
          <transition name="fade" mode="out-in">
                        <span v-if="!errorForm.error">
                            <Icon v-if="!loginLock" type="md-person-add" :key="0"/>
                            <Icon v-else class="green" type="md-checkmark-circle-outline" :key="1"/>
                        </span>
            <span v-else>
                             <Icon class="error" type="ios-close-circle-outline"/>
                        </span>
          </transition>
        </div>
      </div>
      <div class="login-form">
        <Form ref="formValidate" :model="formValidate" :rules="ruleValidate">
          <FormItem prop="username">
            <Input type="text" placeholder="用户" size="large"
                   v-model="formValidate.username">
              <Icon type="ios-person-outline" slot="prepend"></Icon>
            </Input>
          </FormItem>
          <FormItem prop="email">
            <Input type="email" placeholder="邮箱" size="large"
                   v-model="formValidate.email">
              <Icon type="ios-mail-outline" slot="prepend"/>
            </Input>
          </FormItem>
          <FormItem prop="password">
            <Input type="password" placeholder="密码" size="large"
                   v-model="formValidate.password"
                   @keyup.enter="onRegister">
              <Icon type="ios-lock-outline" slot="prepend"></Icon>
            </Input>
          </FormItem>
          <FormItem prop="subpassword">
            <Input type="password" placeholder="重复密码" size="large"
                   v-model="formValidate.subpassword"
                   @keyup.enter="onRegister">
              <Icon type="ios-lock-outline" slot="prepend"></Icon>
            </Input>
          </FormItem>
          <FormItem>
            <Alert type="warning" show-icon v-if="errorForm.error">{{ errorForm.message }}</Alert>

            <span v-if="loginLock">
                            <Button type="info" shape="circle" size="large" long disabled loading
                                    v-on:click="onRegister">以注册，正在跳转
                            </Button>
                        </span>
            <span v-else>
                            <Button v-if="!errorForm.error"
                                    type="info" shape="circle" size="large" long
                                    v-on:click="onRegister">注册
                                </Button>
                            <Button v-else type="info" shape="circle" size="large" long
                                    v-on:click="onRegister" disabled>注册
                                </Button>
                        </span>

          </FormItem>
          <FormItem>
            <div class="float-left">
              <router-link :to="{name: 'login'}">
                <Icon type="ios-arrow-back"/>
                已有账户？ 请登录
              </router-link>
            </div>
            <div class="float-right">
              <router-link :to="{name: 'register'}">注册账户</router-link>
              <span>&nbsp;|&nbsp;</span>
              <router-link to="">忘记密码</router-link>
            </div>

          </FormItem>
        </Form>
      </div>

      <Spin size="large" fix v-if="isValids"></Spin>
    </Card>
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
    Checkbox,
    Spin,
    Alert
  } from 'iview'
  import '@/assets/Icon-tencent/iconfont.css'
  import HeaderIndex from '@/components/auth/HeaderIndex'
  import Axios from '@/axios/index'

  export default {
    name: 'register',
    data () {
      const email = (rule, value, callback) => {
        this.clearErrorForm()
        if (value) {
          return callback()
        }
        return callback(new Error('请输入邮箱'))
      }
      const username = (rule, value, callback) => {
        this.clearErrorForm()
        if (value.length > 2) {
          return callback()
        } else if (value) {
          return callback(new Error('用户名需大于3位数'))
        }
        return callback(new Error('请输入用户'))
      }
      const password = (rule, value, callback) => {
        this.clearErrorForm()
        if (value.length > 6) {
          return callback()
        } else if (value) {
          return callback(new Error('密码长度需大于6位数哦'))
        }
        return callback(new Error('请输入密码'))
      }
      const subpassword = (rule, value, callback) => {
        this.clearErrorForm()
        if (value === this.formValidate.password) {
          return callback()
        } else {
          return callback(new Error('密码不一致'))
        }
      }

      return {
        isValids: false, // 正在登录状态 动画
        loginLock: false, // 登录一把锁 状态同步 isValids， 路由跳转
        setting: {
          loop: true,
          autoplay: true,
          autoplaySpeed: 10000,
          dots: 'none',
          radiusDot: false,
          trigger: 'click',
          arrow: 'never'
        },
        formValidate: {
          email: '',
          username: '',
          password: '',
          subpassword: ''
        },
        errorForm: {
          error: false,
          message: null
        },
        ruleValidate: {
          email: [
            {
              required: true,
              validator: email
            },
            {
              required: true,
              type: 'email',
              message: '您输入的不是邮箱哦！'
            }
          ],
          username: [
            {
              required: true,
              validator: username
            }
          ],
          password: [
            {
              required: true,
              validator: password
            }
          ],
          subpassword: [
            {
              required: true,
              message: '请您输入二次确认密码！'
            },
            {
              required: true,
              validator: subpassword
            }
          ]
        }
      }
    },
    created: function () {
      this.$store.commit('index/setMenuActive', '3')
    },
    methods: {
      /* 注册用户 */
      onRegister: function () {
        this.$refs['formValidate'].validate((valid) => {
          if (valid) {
            this.isValids = true
            Axios.modelPrivateUser('POST', {
              email: this.formValidate.email,
              username: this.formValidate.username,
              password: this.formValidate.password,
              groups: []
            }).then(response => {
              this.isValids = false
              this.loginLock = true
            }).catch(error => {
              this.isValids = false
              if (error.data) {
                /* 可能是用户以注册等错误 */
                if (error.status === 400 && error.data.username) {
                  this.errorForm.error = true
                  this.errorForm.message = error.data.username[0]
                }
              }
            })
          }
        })
      },
      onLogins: function () {
        this.loginLock = true
        console.log('lock')
      },

      clearErrorForm: function () {
        this.errorForm.error = false
        this.errorForm.message = null
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
      Spin,
      Alert,
      'header-index': HeaderIndex
    },
    computed: {
      routerLogin: function () {
        if (this.loginLock) return this.loginLock
      }
    },
    watch: {
      /* 路由延迟跳转,跳转登录页 */
      routerLogin: function (a, b) {
        setTimeout(() => {
          this.$router.push({
            name: 'login'
          })
        }, 1000)
      }
    }

  }
</script>

<style scoped>
  >>> .ivu-form-item-content {
    line-height: 15px;
  }

  >>> .float-left > a .ivu-icon {
    line-height: 0;
  }

  .login-card {
    top: 15vh;
    width: 100vw;
    position: fixed;
  }


  .login-title {
    display: flow-root;
  }

  .login-card-center {
    color: #fff;
    margin-bottom: 45px;
    text-align: center;
  }

  .login-card-center h1 {
    font-weight: 100;
  }

  .login-card-left {
    float: left;
  }

  .login-card-right {
    float: right;
  }

  .login-card-right i {
    font-size: 54px;
    color: #dddddd;
  }

  .login-card-title {
    color: #2c3e50;
    margin-bottom: .75rem;
  }

  .login-card-text:last-child {
    color: #2c3e50;
    font-size: 14px;
    font-weight: 500;
    margin-bottom: 0;
  }

  .login-form {
    padding: 10px;
  }

  /* 登录一把锁，动画 */
  .fade-enter-active, .fade-leave-active {
    transition: opacity .5s
  }

  .fade-enter, .fade-leave-active {
    opacity: 0
  }


  /* 调整其他登录方式 Icon图标大小及位置 */
  >>> .ivu-avatar-large i {
    font-size: 24px;
  }

  >>> .ivu-avatar-large {
    width: 35px;
    height: 35px;
  }

  >>> .ivu-avatar-large span {
    margin: auto;
    text-align: center;
    line-height: 36px !important;
  }

  .green {
    color: #00cf00 !important;
  }

  .error {
    color: crimson !important;
  }
</style>
