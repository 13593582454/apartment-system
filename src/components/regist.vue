<template>
  <div class="login">
    <h1>高校公寓化宿舍管理系统</h1>
    <div class="form">
      <div class="title">帐号注册</div>
      <el-form :model="mes" ref="mes" :rules="rules">
        <el-form-item label="用户名" prop="username">
          <el-input v-model="mes.username" placeholder="请输入用户名"></el-input>
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="mes.password" type="password" placeholder="请输入密码" @keyup.native="ok"></el-input>
        </el-form-item>
        <el-form-item label="请再次输入密码" prop="password2">
          <el-input type="password" v-model="mes.password2" placeholder="请再次输入密码"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" class="w-100p mt-20" @click="submit">注册</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>
<script>
  export default {
    data () {
      return {
        mes: {
          username: '',
          password: '',
          password2: ''
        },
        rules: {
          username: [
            { required: true, message: '用户名不能为空', trigger: 'change' }
          ],
          password2: [
            { required: true, message: '请再次输入密码', trigger: 'change' }
          ],
          password: [
            { required: true, message: '密码不能为空', trigger: 'change' }
          ]
        }
      }
    },
    methods: {
      submit () {
        this.$refs.mes.validate((valid) => {
          if (valid) {
            if (this.mes.password2 != this.mes.password) {
              this.$message.error('两次输入的密码不一致');
              return false;
            }
            const params = {
              username: this.mes.username,
              password: this.mes.password
            };
            this.$post(host + 'regist', params).then(res => {
              if (res == '用户名已存在') {
                this.$message.error('用户名已存在');
              } else if (res == 0) {
                this.$message.error('注册失败');
              } else {
                this.$message.success('注册成功');
                sessionStorage.loginUser = JSON.stringify(res[0]);
                this.$router.push('/students');
              }
            });
          } else {
            // console.log('error submit!!');
            return false;
          }
        });
      },
      ok (e) {
        if (e.keyCode == 13) {
          this.submit();
        }
      }
    },
    created () {
      this.$store.commit('changeLoginState', true);
    },
    destroyed () {
      this.$store.commit('changeLoginState', false);
    }
  }
</script>
<style lang="scss" scoped>
  @import '../scss/base.scss';
  .login {
    height: 100%;
    background: $baseColor!important;
    h1 {
      text-align: center;
      color: #fff;
      padding: 100px 0 50px 0;
    }
    .form {
      border-radius: 5px;
      box-shadow: 0 0 10px #666;
      background: #fff;
      margin: 0 auto;
      width: 320px;
      padding: 45px 90px 45px 90px;
      border: 1px solid #ddd;
      .title {
        color: $baseColor;
        font-weight: bold;
        font-size: 28px;
        text-align: center;
        margin-bottom: 20px;
      }
    }
  }
</style>
