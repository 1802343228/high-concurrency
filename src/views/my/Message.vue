<template>
  <v-app>
    <nav-bar></nav-bar>
    <div class="message">
      <div class="fengmian"></div>
      <div class="biankuang">
        <v-hover>
          <template v-slot:default="{ hover }">
            <v-card class="avatar" max-width="200" max-height="200">
              <img style="height: 200px; width: 200px" :src="user.avatar" />
              <v-fade-transition>
                <v-overlay v-if="hover" absolute color="#f5f5f5">
                  <v-btn @click="avatarClick()"
                    >修改头像
                    <input
                      type="file"
                      @change="uploadAvatar($event)"
                      ref="file"
                      style="display: none"
                      id="file"
                      accept="image/jpg, image/jpeg, image/png"
                    />
                  </v-btn>
                </v-overlay>
              </v-fade-transition>
            </v-card>
          </template>
        </v-hover>

        <div class="btn-col">
          <button @click="submit()" class="btn">提交</button>
        </div>
        <div class="xinxi">
          <span style="display: flex">
            <v-text-field
              v-if="userInput"
              label="用户名"
              placeholder="请输入用户名.."
              v-model="user.nickname"
            ></v-text-field>
            <h1 v-else>{{ user.nickname }}</h1>
            <img
              @click="updateUser()"
              style="width: 30px; height: 30px; margin: 10px"
              src="../../assets/icon/bi.png"
            />
          </span>
          <span style="display: flex; margin-top: 50px">
            <h4>电话</h4>
            <span class="label">
              <p>{{ user.phone }}</p>
            </span>
          </span>
          <div class="line"></div>

          <span style="display: flex; margin-top: 50px">
            <h4>密码</h4>
            <span class="label">
              <p>
                <span>**********</span>
                <span
                  @click="updateCode()"
                  style="color: #26a69a; margin-left: 20px"
                  >更改</span
                >
                <v-text-field
                  v-if="codeInput"
                  label="密码"
                  placeholder="请输入密码.."
                  v-model="validate.password"
                ></v-text-field>
              </p>
            </span>
          </span>
          <div class="line"></div>

          <span style="display: flex; margin-top: 50px">
            <h4>邮箱</h4>
            <span class="label">
              <p>
                <span>{{ user.email }}</span>
                <span
                  @click="updateEmail()"
                  style="color: #26a69a; margin-left: 20px"
                  >更改</span
                >
                <v-text-field
                  v-if="emailInput"
                  label="邮箱"
                  placeholder="请输入邮箱.."
                  v-model="user.email"
                ></v-text-field>
              </p>
            </span>
          </span>
          <div class="line"></div>

          <span style="display: flex; margin-top: 50px">
            <h4>性别</h4>
            <span class="label">
              <label
                ><input type="radio" name="sex" v-model="user.sex" />男</label
              >
              <label style="margin-left: 20px"
                ><input type="radio" name="sex" v-model="user.sex" />女</label
              >
            </span>
          </span>
          <div class="line"></div>

          <span style="display: flex; margin-top: 50px">
            <h4>余额</h4>
            <span class="label">
              <p>
                <span>{{ user.money }}</span>
                <span style="color: #26a69a; margin-left: 20px">充值</span>
              </p>
            </span>
          </span>
          <div class="line"></div>

          <span style="display: flex; margin-top: 50px">
            <h4>地址</h4>
            <span class="label">
              <span>{{ user.address }}</span>
              <span
                @click="updateAddress()"
                style="color: #26a69a; margin-left: 20px"
                >更改</span
              >
              <v-text-field
                v-if="addressInput"
                label="地址"
                placeholder="Placeholder"
                filled
                v-model="user.addresss"
              ></v-text-field>
            </span>
          </span>
          <div class="line"></div>
        </div>
      </div>
    </div>
  </v-app>
</template>

<script>
import NavBar from "../../components/NavBar";
const API = require("../../utils/request.js");
const clickoutside = {
  // 初始化指令
  bind(el, binding) {
    function documentHandler(e) {
      // 这里判断点击的元素是否是本身，是本身，则返回
      if (el.contains(e.target)) {
        return false;
      }
      // 判断指令中是否绑定了函数
      if (binding.expression) {
        // 如果绑定了函数 则调用那个函数，此处binding.value就是handleClose方法
        binding.value(e);
      }
    }
    // 给当前元素绑定个私有变量，方便在unbind中可以解除事件监听
    el.__vueClickOutside__ = documentHandler;
    document.addEventListener("click", documentHandler);
  },
  update() {},
  unbind(el) {
    // 解除事件监听
    document.removeEventListener("click", el.__vueClickOutside__);
    delete el.__vueClickOutside__;
  },
};
export default {
  name: "HomePage",
  data() {
    return {
      userInput: false,
      emailInput: false,
      addressInput: false,
      codeInput: false,
      overlay: false,
      user: [],
      validate: {
        nickname: "",
        sex: "",
        avatar: "",
        addresss: "",
        email: "",
        password: "",
      },
    };
  },
  components: {
    NavBar,
  },
  mounted: function () {
    const id = localStorage.getItem("userId");
    console.log("1111111");
    this.axios
      .get(this.GLOBAL.baseUrl + "/user/getInfoById/" + id)
      .then((res) => {
        console.log(res);
        this.user = res.data.data;
      });
  },
  directives: { clickoutside },
  methods: {
    updateUser() {
      this.userInput = !this.userInput;
    },
    updateEmail() {
      this.emailInput = !this.emailInput;
    },
    updateAddress() {
      this.addressInput = !this.addressInput;
    },
    updateCode() {
      this.codeInput = !this.codeInput;
    },
    avatarClick() {
      this.$refs.file.click();
    },
    async submit() {
      let sex;
      const _this = this;
      let users = _this.user;
      if (_this.validate.sex === "男") {
        sex = 1;
      } else if (_this.validate.sex === "女") {
        sex = 2;
      }
      let pwd;
      if (_this.validate.password === "") {
        pwd = "123";
      } else {
        pwd = _this.validate.password;
      }

      _this.url = _this.GLOBAL.baseUrl + "/user/edit";

      _this.data = {
        address: users.addresss,
        avatar: _this.validate.avatar,
        code: "",
        email: users.email,
        nickname: users.nickname,
        password: pwd,
        phone: users.phone,
        pkHbUserId: users.pkUserId,
        sex: sex,
        birthday: "",
        username: users.username,
      };
      await API.init(_this.url, _this.data, "post");
      console.log("修改成功")
      windows.alert("修改成功")
    },
    updateAdminInfo(url) {
      console.log(url);
      this.validate.avatar = url;
      this.user.avatar = url;
    },
    uploadAvatar(event) {
      console.log("111111111");
      const OSS = require("ali-oss");
      let client = new OSS({
        region: "oss-cn-beijing",
        accessKeyId: "LTAI4GD8r7BPa4ik89fSdFws",
        accessKeySecret: "H5uLKRHHYnndxuHctQjPPBJj5vRWSH",
        bucket: "nttbucket",
      });
      let timestamp = Date.parse(new Date());
      let imgUrl = "img/" + timestamp + "." + "jpeg";
      var file = event.target.files[0]; //获取文件流
      var _this = this;
      client.multipartUpload(imgUrl, file).then(function (result) {
        _this.validate.avatar = result.res.requestUrls[0];
        _this.updateAdminInfo(_this.validate.avatar);
        console.log("222222");
        console.log(_this.validate.avatar);
      });
    },
  },
};
</script>
<style lang="scss" scoped>
.message {
  width: 85%;
  margin: auto;
  box-shadow: #26a69a 0 0 2px 1px;
  border-radius: 10px;
}
.fengmian {
  background-color: #26a69a;
  height: 300px;
  border-radius: 10px 10px 0 0;
}
.avatar {
  width: 200px;
  height: 200px;
  border-radius: 10px;
  margin-top: -50px;
  margin-left: 50px;
}
.xinxi {
  margin: 30px;
}
.line {
  border: 0.1px solid rgb(240, 237, 237);
  width: 270%;
  margin-top: 30px;
}
.label {
  margin-left: 100px;
}
.biankuang {
  display: flex;
}
.btn {
  width: 80px;
  height: 40px;
  background-color: #26a69a;
  color: #ffffff;
  font-size: 1.3rem;
  border-radius: 10px;
  border: 0; // 去除未选中状态边框
  outline: none; // 去除选中状态边框
}
.btn-col {
  position: absolute;
  right: 20%;
  top: 40%;
}
</style>