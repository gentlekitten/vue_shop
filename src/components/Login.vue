<template>
  <div class="login_container">
   <div class="login_box">
     <!-- 头像区域 -->
     <div class="avatar_box">
       <img src="../assets/logo.png" />
     </div>
     <!-- 登录区域 -->
     <el-form label-width="0px" class="login_form" :model="loginForm" :rules="loginFormRules" ref="loginFormRef">
       <!-- 用户 -->
       <el-form-item prop="username">
         <el-input prefix-icon="iconfont icon-user" v-model="loginForm.username"></el-input>
       </el-form-item>
       <!-- 密码 -->
       <el-form-item prop="password">
         <el-input prefix-icon="iconfont icon-lock_fill" v-model="loginForm.password" type="password"></el-input>
       </el-form-item>
       <!-- 按钮区域 -->
       <el-form-item class="btns">
         <el-button type="primary" @click="login">登录</el-button>
         <el-button type="info" @click="resetloginFormRef">重置</el-button>
       </el-form-item>
     </el-form>
   </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      // 登录表单的数据绑定
      loginForm: {
        username: 'admin',
        password: '123456'
      },
      // 表单的验证规则对象
      loginFormRules: {
        // 用户验证
        username: [
          { required: true, message: '请输入用户名称', trigger: 'blur' },
          { min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        // 密码验证
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 6, max: 15, message: '长度在 6 到 15 个字符', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    // 点击重置表单
    resetloginFormRef () {
      // console.log(this)
      this.$refs.loginFormRef.resetFields()
    },
    // 点击登录按钮验证
    login () {
      this.$refs.loginFormRef.validate(async valid => {
        if (!valid) return
        // 当返回值是promise时要用await简化，而await只能用在被async修饰的方法中
        const { data: res } = await this.$http.post('login', this.loginForm)
        console.log(res)
        if (res.meta.status !== 200) return this.$message.error('登录失败！')
        this.$message.success('登录成功！')
        // 1.将登录成功后的token，保存到客户端的sessionStorage中
        // 1.1项目中出了登录以外的其他API接口，必须在登录之后才能访问
        // 1.2token只应在当前网站打开期间生效，所以将token保存在sessionStorage中
        window.sessionStorage.setItem('token', res.data.token)
        // 2.通过编程式导航跳转到后台主页，路由地址是/home
        this.$router.push('/home')
      })
    }
  }
}
</script>

<style lang="less" scoped>
.login_container {
  background-color: #2b4b6b;
  height: 100%;
}
.login_box {
  width: 450px;
  height: 300px;
  background-color: #fff;
  border-radius: 3px;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%,-50%);
}
.avatar_box {
  height: 130px;
  width: 130px;
  border: 1px solid #eee;
  border-radius: 50%;
  padding: 10px;
  box-shadow: 0 0 10px #ddd;
  position: absolute;
  left: 50%;
  transform: translate(-50%,-50%);
  background-color: #fff;
  img {
    width: 100%;
    height: 100%;
    border-radius: 50%;
    background-color: #eee;
  }
}
.btns {
  display: flex;
  justify-content: flex-end;
}
.login_form {
  position: absolute;
  bottom: 0;
  width: 100%;
  padding: 0 20px;
  box-sizing: border-box;
}
</style>
