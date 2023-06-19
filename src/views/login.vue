<!--
 * @Description:: 请补充填写
 * @Version: 1.0
 * @Author: smallWhite
 * @Date: 2023-03-21 21:29:37
 * @LastEditors: smallWhite
 * @LastEditTime: 2023-04-23 16:32:38
 * @FilePath: /chat_gpt/src/views/login.vue
-->
<template>
  <div class="s">

    <div class="login">
      <div style="width:60px;height:60px">
<!--        <img-->
<!--          :src="require('../assets/logo1.png')"-->
<!--          style="width:60px;height:60px">-->
      </div>
      <h2 v-if="showPage" style="color: #fff">欢迎登录
      </h2>
      <h2 v-if="!showPage" style="color: #fff">
        用户注册
      </h2>
      <el-card class="forms"
        v-if="showPage"
        shadow="never">

        <el-form :model="loginForm"
          :rules="rules"
          ref="loginForm"
          label-width="70px"
          class="demo-ruleForm">
          <el-form-item label="邮箱" prop="email">
            <el-input prefix-icon="el-icon-message" placeholder="请填写邮箱" maxlength="32" v-model="loginForm.email" ></el-input>
          </el-form-item>

          <el-form-item label="密码" prop="password"  v-show="!isCode">
            <el-input prefix-icon="el-icon-lock" placeholder="请填写 6-18 位密码" type="password" maxlength="18" v-model="loginForm.password" show-password></el-input>
          </el-form-item>
          <el-form-item
            style="margin-bottom: 0;">
            <el-button
              type="primary"
              @click="login"
              style="width:100%">登录</el-button>
          </el-form-item>

          <el-form-item
            style="margin-bottom: 0;">
          </el-form-item>
        </el-form>
      </el-card>


      <el-card class="forms"
        shadow="never"
        v-if="!showPage">

        <el-form :model="registerForm" :rules="rules" ref="registerForm" label-width="70px">

          <el-form-item label="邮箱" prop="email">
            <el-input prefix-icon="el-icon-message" placeholder="请填写邮箱" maxlength="32" v-model="registerForm.email" ></el-input>
          </el-form-item>

          <el-form-item label="密码" prop="password">
            <el-input prefix-icon="el-icon-lock" placeholder="请填写 6-18 位密码" type="password" maxlength="18" v-model="registerForm.password" show-password></el-input>
          </el-form-item>

          <el-form-item label="确认密码" prop="passwordConfirm">
            <el-input prefix-icon="el-icon-lock" placeholder="请填写 6-18 位密码" type="password" maxlength="18" v-model="registerForm.passwordConfirm" show-password></el-input>
          </el-form-item>

          <el-form-item label="验证码" prop="code">
            <el-input prefix-icon="el-icon-key" placeholder="请填写6位数字验证码" type="number" maxlength="6" v-model="registerForm.code">
              <el-button slot="append" @click="sendEmailCode()" :disabled="disabled">{{ msg }}</el-button>
            </el-input>
          </el-form-item>

          <el-form-item
              style="margin-bottom: 0;">
            <el-button
                type="primary"
                @click="register"
                style="width:100%">注册</el-button>
          </el-form-item>

          <el-form-item
              style="margin-bottom: 0;">
          </el-form-item>
        </el-form>
      </el-card>
      <el-button
        v-if="showPage"
        @click="reg"
        style="width:445px;margin-top:30px">还没有账号？立即注册</el-button>
      <el-button
        v-if="!showPage"
        @click="reg"
        style="width:445px;margin-top:30px">已有账号，立即登录</el-button>

      <!-- 找回密码 -->
<!--      <el-dialog @close="clearForm('findPasswordForm')" title="找回密码" :visible.sync="dialogFormVisible" width="40%">-->
<!--        <el-form :model="findPasswordForm" ref="findPasswordForm" :rules="rules" label-width="60px">-->

<!--          <el-form-item label="邮箱" prop="email">-->
<!--            <el-input prefix-icon="el-icon-message" placeholder="请填写邮箱" maxlength="32" v-model="findPasswordForm.email"></el-input>-->
<!--          </el-form-item>-->

<!--          <el-form-item label="新密码" prop="password">-->
<!--            <el-input prefix-icon="el-icon-lock" placeholder="请填写 6-18 位密码" type="password" maxlength="18" v-model="findPasswordForm.password" show-password></el-input>-->
<!--          </el-form-item>-->

<!--          <el-form-item label="验证码" prop="code">-->
<!--            <el-input prefix-icon="el-icon-key" placeholder="请填写6位数字验证码" type="number" maxlength="6" v-model="findPasswordForm.code">-->
<!--              <el-button slot="append" @click="sendEmailCode()" :disabled="disabled">{{ msg }}</el-button>-->
<!--            </el-input>-->
<!--          </el-form-item>-->
<!--        </el-form>-->

<!--        <div slot="footer" class="dialog-footer">-->
<!--          <el-button type="danger" :loading="loading" @click="submitFindPassword('findPasswordForm')">提 交</el-button>-->
<!--        </div>-->
<!--      </el-dialog>-->

    </div>
  </div>
</template>

<script>
import accountVue from './components/account.vue'
import simcodeVue from './components/simcode.vue'
import mailcodeVue from './components/mailcode.vue'
export default {
  components: { accountVue, simcodeVue, mailcodeVue },
  data() {
    const email = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('请输入邮箱！'))
      } else if (!/^([a-zA-Z0-9]+[-_\.]?)+@[a-zA-Z0-9]+\.[a-z]+$/.test(value)) {
        return callback(new Error('请输入正确的邮箱！'))
      } else {
        return callback()
      }
    };
    const password = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('请输入密码！'))
      } else if (!/(?=.*[a-zA-Z])[a-zA-Z0-9]{6,18}/.test(value)) {
        return callback(new Error("密码长度在6-18个字符，只能包含数字、大小写字母 且 至少包含一个字母！"))
      } else if (this.registerForm.passwordConfirm !== '') {
        this.$refs.registerForm.validateField('passwordConfirm');
        return callback()
      } else {
        return callback()
      }
    };
    const passwordConfirm = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('请输入确认密码！'))
      } else if (!/(?=.*[a-zA-Z])[a-zA-Z0-9]{6,18}/.test(value)) {
        return callback(new Error("密码长度在6-18个字符，只能包含数字、大小写字母 且 至少包含一个字母！"))
      } else if (value !== this.registerForm.password) {
        return callback(new Error('两次输入的密码不一致！'))
      } else {
        return callback()
      }
    };
    const code = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('请输入6位验证码！'))
      } else if (value.length !== 6) {
        return callback(new Error('请输入6位验证码！'))
      } else {
        return callback()
      }
    };
    return {
      isCode: false,
      showPage: true,
      loginForm: {
        email: '',
        password: ''
      },
      registerForm: {
        email: '',
        password: '',
        passwordConfirm: '',
        code: ''
      },
      disabled: false,
      msg: '点击获取验证码',
      count: 60,
      timer: 0,
      // 按钮加载
      loading: false,
      regs: 0,
      rules: {
        email: [
          { validator: email, trigger: 'blur' }
        ],
        password: [
          { validator: password, trigger: 'blur' }
        ],
        passwordConfirm: [
          { validator: passwordConfirm, trigger: 'blur'}
        ],
        code: [
          { validator: code, trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    reg() {
      this.showPage = !this.showPage
    },
    // 登录
    login() {
      this.$refs.loginForm.validate(valid => {
        if (valid) {
          this.$https('LOGIN', this.loginForm).then(res => {
            if (res.status != 200) {
              this.$alert(res.message, '提示')
            } else {
              window.localStorage.setItem('userInfo', JSON.stringify(res.data))
              window.localStorage.setItem('token', res.data.token)
              this.$message.success('登录成功！')
              if (this._isMobile()) {
                window.localStorage.setItem('phone', true)
              } else {
                window.localStorage.setItem('phone', false)
              }
              setTimeout(() => {
                this.$router.push('/')
              }, 2000)
            }
          })
        }
      })
    },
    // 注册
    register() {
      this.$refs.registerForm.validate((valid) => {
        if (valid) {
          this.loading = true
          this.$https('REGISTER', this.registerForm).then(_ => {
            if(_) {
              // 注册成功
              this.reg()

              this.$message({
                message: '注册成功',
                type: 'success'
              })
            }
          }).finally(_ => {
            this.loading = false
          })
        }
      });
    },
    // 获取验证码
    sendEmailCode() {
      this.$refs.registerForm.validateField('email', result => {
        if(!result) {
          // 按钮倒计时
          this.disabled = true;
          this.msg = this.count-- + 's后重新获取';
          this.timer = setInterval(() => {
            this.msg = this.count-- + 's后重新获取';
            if (this.count < 0) {
              this.msg = '点击获取验证码';
              this.count = 60;
              this.disabled = false;
              clearInterval(this.timer);
            }
          }, 1000);

          // 发送验证码请求
          common.getRequestCode(this.registerForm.email).then(_ => {
            common.getEmailCode(this.registerForm.email, _.data.permissionCode).then(_ => {
              if(_) {
                // 通知邮箱发送
                this.$notify({
                  title: '邮箱验证码已发送',
                  message: '验证码有效时长5分钟, 失效请重新发送',
                  type: 'success',
                  duration: 10 * 1000
                })
              }
            })
          })
        }
      });
    },
  }
}
</script>

<style lang="scss">
.login {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background: url('../assets/bg.jpg') fixed no-repeat;
  background-size: 100% 100%;
  display: flex;
  align-items: center;
  flex-flow: column;
  .logo {
    margin-top: 50px;
    background: #fff;
  }
  .forms {
    width: 445px;
    background: #f6f8fa;
  }
}
.el-form--label-top .el-form-item__label {
  padding: 0;
  line-height: 30px;
}

.el-input-group__append button.el-button,
.el-input-group__append div.el-select .el-input__inner,
.el-input-group__append div.el-select:hover .el-input__inner,
.el-input-group__prepend button.el-button,
.el-input-group__prepend div.el-select .el-input__inner,
.el-input-group__prepend div.el-select:hover .el-input__inner {
  background: #409eff;
  color: #fff;
}
.el-button--primary.is-disabled {
  background: #a5d2ff !important;
  color: #fff !important;
}
</style>