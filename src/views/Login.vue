<template>
  <div class="login-wrap">
    <div class="login-box">
      <a href="/"><img src="../assets/images/nice_links.png" alt="nice links logo"></a>
      <div class="form-group">
        <el-alert
          v-if="tipMessageObj.message"
          :title="tipMessageObj.message"
          :type="tipMessageObj.type">
        </el-alert>
      </div>
      <el-form :model="account" :rules="rules" ref="validateForm">
        <el-form-item prop="email">
          <el-input v-model="account.email" placeholder="Your email address"
            @keydown.enter.native="onLoginClick">
            <template slot="prepend"><icon class="icons" name="login-email"></icon></template>
          </el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input v-model="account.password" type="password"
            placeholder="Create a password" @keydown.enter.native="onLoginClick">
            <template slot="prepend"><icon class="icons" name="password"></icon></template>
          </el-input>
        </el-form-item>
        <el-button type="primary" :loading="isLoading"
          @click="onLoginClick" size="large">{{ $t('signIn') }}</el-button>
        <el-button :loading="isLoading"
          @click="onSignupClick" size="large">{{ $t('signUp') }}</el-button>
        <el-button type="text" :loading="isLoading"
          @click="onForgotPwdClick" size="large">{{ $t('forgetPwd') }}</el-button>
      </el-form>
    </div>
  </div>
</template>

<script>
  export default{
    data () {
      return {
        isLoading: false,
        tipMessageObj: {},
        account: {
          email: '',
          password: ''
        },
        rules: {
          email: [
            {
              required: true,
              message: '请输入邮箱',
              trigger: 'change,blur'
            }
          ],
          password: [
            {
              required: true,
              message: '请输入密码',
              trigger: 'change,blur'
            }
          ]
        }
      }
    },

    mounted () {
    },

    components: {
    },

    computed: {
    },

    methods: {
      composeParams () {
        return {
          email: this.account.email,
          password: this.$util.encryptPwd(this.account.password)
        }
      },

      // ----------------------------onClickEvent-----------------------------
      onLoginClick () {
        this.$refs['validateForm'].validate((valid) => {
          if (valid) {
            this.isLoading = false
            this.$apis.login(this.composeParams()).then(result => {
              this.isLoading = false

              // save user-id into vuex-state(& localStorage)
              this.$store.commit('$vuexSetUserInfo', {_id: result._id})

              this.$router.push('/')
            }).catch(error => {
              debugger
              this.isLoading = false
              this.tipMessageObj = {
                message: error,
                type: 'error'
              }
            })
          } else {
            return false
          }
        })
      },

      onSignupClick () {
        this.$refs['validateForm'].validate((valid) => {
          if (valid) {
            this.isLoading = false
            this.$apis.signup(this.composeParams()).then(result => {
              this.tipMessageObj = {
                message: result,
                type: 'success'
              }
            }).catch((error) => {
              this.isLoading = false
              this.tipMessageObj = {
                message: error,
                type: 'error'
              }
            })
          } else {
            return false
          }
        })
      },

      onForgotPwdClick () {
        this.$router.push('forgot-pwd')
      }
    }
  }
</script>

<style lang="scss">
@import "../assets/scss/variables.scss";
@import "../assets/scss/mixins.scss";

.login-wrap {
  width: 520px;
  margin: 0 auto;
  padding-top: 150px;
  min-height: 400px;
  position: relative;
}
.login-box {
  width: 520px;
  padding: 30px;
  height: 100%;
  background-color: #fff;
  clear: both;
  display: table;
  border-radius: 3px;
  border: 1px solid #d7dce5;
  .heading{
    text-align: center;
    margin-bottom: 30px;
    font-size: 24px;
    color: #707473;
  }
  img{
    display: block;
    margin: 0 auto 20px;
  }
  .el-form-item{
    margin-bottom: 30px;
  }
  .el-button{
    display: block;
    width: 100%;
    margin: 0 auto;
    margin-bottom: 30px;
  }
  .el-input{
    .icons{
      display: block;
      width: 20px;
      height: 20px;
      margin: 0;
      padding: 0;
    }
  }
}

@media (max-width: 500px) {
  .login-wrap {
    width: 100%;
    padding-top: 60px;
  }
  .login-box {
    width: 100%;
    border: 0 none;
  }
}
</style>
