<template>
  <div class="login">
    <LoginHeader>
      <el-form slot="container"
               :model="ruleForm"
               label-position="left"
               label-width="0px"
               :rules="rules"
               ref="ruleForm">
        <div class="title">
          <h3>账号密码登录</h3>
        </div>
        <!-- username -->
        <el-form-item prop="username">
          <el-input type="text"
                    v-model="ruleForm.username"
                    auto-complete="off"
                    placeholder="账号">
            <i slot="prefix"
               class="fa fa-user-o"></i>
          </el-input>
        </el-form-item>
        <!-- password -->
        <el-form-item prop="pwd">
          <el-input type="password"
                    v-model="ruleForm.pwd"
                    auto-complete="off"
                    placeholder="密码">
            <i slot="prefix"
               class="fa fa-lock"></i>
          </el-input>
        </el-form-item>
        <!-- 登录 -->
        <el-form-item>
          <el-button type="primary"
                     style="width:100%"
                     @click.native.prevent="handleSubmit"
                     :loading="isLogin">登录</el-button>
        </el-form-item>

        <!-- 7天登录，忘记密码 -->
        <el-form-item>
          <el-checkbox v-model="ruleForm.autoLogin"
                       :checkout="ruleForm.autoLogin">7天内自动登录</el-checkbox>
          <el-button @click="$router.push('/password')"
                     type="text"
                     class="forget">忘记密码?</el-button>
        </el-form-item>
      </el-form>
    </LoginHeader>
  </div>
</template>
<script lang="ts">
import { Component, Vue, Provide } from "vue-property-decorator";
import { State, Getter, Mutation, Action } from "vuex-class";
import LoginHeader from "./LoginHeader.vue";
@Component({
  components: {
    LoginHeader
  }
})
export default class Login extends Vue {
  @Provide() isLogin: Boolean = false;
  @Action("setUser") setUser: any;
  @Provide() ruleForm: {
    username: String;
    pwd: String;
    autoLogin: Boolean;
  } = {
    username: "",
    pwd: "",
    autoLogin: true
  };
  @Provide() rules = {
    username: [{ required: true, message: "请输入账号", trigger: "blur" }],
    pwd: [{ required: true, message: "请输入密码", trigger: "blur" }]
  };

  handleSubmit(): void {
    (this.$refs["ruleForm"] as any).validate((valid: boolean) => {
      if (valid) {
        this.isLogin = true;
        (this as any).$axios
          .post("/api/users/login", this.ruleForm)
          .then((res: any) => {
            // 存储token
            let token = res.data.token;
            localStorage.setItem("tsToken", token);
            // 存储到vuex
            this.setUser(token);
            this.isLogin = false;
            this.$router.push("/");
          })
          .catch(() => {
            this.isLogin = false;
          });
      }
    });
  }
}
</script>

<style lang="scss" scoped>
.title {
  margin: 0px auto 40px auto;
  text-align: center;
  color: #505458;
}

i {
  font-size: 14px;
  margin-left: 8px;
}
.forget {
  float: right;
}
</style>