<template>
  <div class="login">
    <div class="left">
      <div class="title">
        <img src="@/assets/images/11.png" alt />
        <span class="title_name1">黑马面面</span>
        <span class="title_cen">|</span>
        <span class="title_name2">用户登录</span>
      </div>
      <!-- 表单 -->
      <el-form :model="form" :rules="rules" ref="form">
        <el-form-item prop="phone">
          <el-input v-model="form.phone" placeholder="请输入手机号码" prefix-icon="el-icon-user" clearable></el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input
            v-model="form.password"
            :show-password="true"
            placeholder="请输入密码"
            prefix-icon="el-icon-lock"
            clearable
          ></el-input>
        </el-form-item>
        <el-form-item prop="code">
          <el-row>
            <el-col :span="18">
              <el-input
                prefix-icon="el-icon-key"
                v-model="form.code"
                placeholder="请输入验证码"
                clearable
              ></el-input>
            </el-col>
            <el-col :span="6">
              <img @click="codeClick" class="code" :src="codeUrl" alt />
            </el-col>
          </el-row>
        </el-form-item>
        <el-form-item prop="key">
          <el-checkbox v-model="form.key">
            我已阅读并同意
            <el-link type="primary">用户协议</el-link>和
            <el-link type="primary">隐私条款</el-link>
          </el-checkbox>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" class="btn" @click="submitForm">登录</el-button>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" class="btn" @click="model">注册</el-button>
        </el-form-item>
      </el-form>
    </div>
    <div class="right">
      <img src="@/assets/images/login_banner_ele.png" alt />
    </div>
    <!-- 模态框 -->
    <model ref="son_model"></model>
  </div>
</template>

<script>
import { userLogin } from "@/api/login.js";
import { setToken, getToken } from "@/utils/token.js";
import model from "./model.vue";
export default {
  name: "login",
  components: {
    model
  },
  data() {
    return {
      codeUrl: process.env.VUE_APP_URL + "/captcha?type=login",
      form: {
        phone: "",
        password: "",
        code: "",
        key: ""
      },
      rules: {
        phone: [
          { required: true, message: "请输入手机号", trigger: "blur" },
          {
            validator: (rule, value, callback) => {
              let reg = /^(0|86|17951)?(13[0-9]|15[012356789]|166|17[3678]|18[0-9]|14[57])[0-9]{8}$/;
              if (reg.test(value)) {
                callback();
              } else {
                callback("手机格式不对");
              }
            }
          }
        ],
        password: [
          { required: true, message: "请输入密码", trigger: "blur" },
          {
            min: 6,
            max: 12,
            message: "正确输入密码，6-12位",
            trigger: "change"
          }
        ],
        code: [
          { required: true, message: "请输入验证码", trigger: "blur" },
          {
            min: 4,
            max: 4,
            message: "正确输入验证码，白痴4位",
            trigger: "change"
          }
        ],
        key: [
          { required: true, message: "请勾选选协议", trigger: "change" },
          {
            validator: (rule, value, callback) => {
              if (value === true) {
                callback();
              } else {
                callback("请勾选选协议");
              }
            },
            trigger: "change"
          }
        ]
      }
    };
  },
  methods: {
    //刷新验证码
    codeClick() {
      this.codeUrl =
        process.env.VUE_APP_URL + "/captcha?type=login" + Date.now();
    },
    //登录请求
    submitForm() {
      this.$refs.form.validate(valid => {
        if (valid) {
          userLogin(this.form).then(res => {
            // console.log(res);
            setToken(res.data.token);
            // this.$message.success("恭喜你，登录成功");
            this.$notify({
              title: "成功",
              message: "恭喜你，登录成功",
              type: "success"
            });
            //登录成功调到home页面
            this.$router.push("/home");
          });
        } else {
          this.$message.warning("白痴！");
        }
      });
    },
    model() {
      this.$refs.son_model.dialogFormVisible = true;
    }
  },
  // 有token直接去首页
  created() {
    if (getToken()) {
      this.$router.push("/home");
    }
  }
};
</script>

<style lang="less" scoped>
.login {
  height: 100%;
  display: flex;
  justify-content: space-around;
  align-items: center;
  background: linear-gradient(
    225deg,
    rgba(20, 147, 250, 1),
    rgba(1, 198, 250, 1)
  );
  .left {
    width: 478px;
    height: 550px;
    background: rgba(245, 245, 245, 1);
    padding: 47px 41px 86px 43px;
    .title {
      margin-bottom: 30px;
      .title_name1 {
        margin: 0 8px;
        font-size: 24px;
        font-weight: 400;
        color: rgba(12, 12, 12, 1);
      }
      .title_cen {
        font-size: 24px;
        color: #ccc;
      }
      .title_name2 {
        margin: 0 8px;
        font-size: 22px;
        font-weight: 400;
        color: rgba(12, 12, 12, 1);
      }
    }
    .code {
      width: 100%;
      height: 40px;
    }
    .btn {
      width: 100%;
    }
  }
}
</style>