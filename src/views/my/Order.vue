<template>
  <v-app>
    <nav-bar></nav-bar>
    <div class="order">
      <v-card>
        <v-tabs
          v-model="tab"
          background-color="teal light-1"
          dark
          icons-and-text
        >
          <v-tabs-slider></v-tabs-slider>

          <v-tab href="#tab-1"> 全部订单 </v-tab>

          <v-tab href="#tab-2"> 待付款 </v-tab>

          <v-tab href="#tab-3"> 待评价 </v-tab>
        </v-tabs>

        <v-tabs-items v-model="tab">
          <v-tab-item v-for="i in 3" :key="i" :value="'tab-' + i">
            <v-card flat>
              <div class="order-border">
                <span style="margin-left: 50px">订单详情</span>
                <span style="margin-left: 400px">数量</span>
                <span style="margin-left: 150px">收货人</span>
                <span style="margin-left: 150px">金额</span>
                <span style="margin-left: 150px">状态</span>
              </div>
              <div
                v-for="(item, index) in order"
                :key="index"
                class="order-one"
              >
                <div class="order-border">
                  <span style="margin-left: 50px; color: gray">{{
                    item.createTime
                  }}</span>
                  <span style="margin-left: 50px; color: gray">{{
                    item.pkOrderId
                  }}</span>
                </div>
                <img
                  style="
                    margin-left: 50px;
                    margin-top: 20px;
                    width: 100px;
                    height: 100px;
                  "
                  src="../../assets/images/me.jpg"
                />
                <span style="margin-left: 50px">{{ item.goodName }}</span>
                <span style="margin-left: 260px">x{{ item.count }}</span>
                <span style="margin-left: 150px">{{ item.userName }}</span>
                <span style="margin-left: 150px">{{ item.money }}</span>
                <span style="margin-left: 150px">{{ item.status }}</span>
              </div>

              <!-- <v-card-text>{{ text }}</v-card-text> -->
            </v-card>
          </v-tab-item>
        </v-tabs-items>
      </v-card>
    </div>
  </v-app>
</template>

<script>
import NavBar from "../../components/NavBar";
const time = require( "../../utils/time.js");
export default {
  name: "HomePage",
  data() {
    return {
      tab: null,
      order: [],
    };
  },
  mounted: function () {
    let params = new URLSearchParams();
    let id = localStorage.getItem("userId");
    console.log(id);
    params.append("pkUserId", id);
    this.axios
      .post(this.GLOBAL.contentUrl + "/order/findUserAllOrder", params)
      .then((res) => {
        // this.order = res.data.data.data;
        let arr = res.data.data.data;
        // for (let j = 0, len = arr.length; j < len; j++) {
        //   console.log(arr[j].createTime)
        //   arr[j].createTime =time.timeFmt(arr[j].createTime)
        // }
        this.order = arr
        console.log(this.order);
      });
  },
  components: {
    NavBar,
  },
};
</script>
<style lang="scss" scoped>
.order {
  width: 85%;
  margin: 0 auto;
}
.order-one {
  border: 1px solid rgb(228, 225, 225);
  height: 200px;
  margin-top: 10px;
}
.order-border {
  height: 40px;
  background-color: #f3f3f3;
  padding: 5px;
}
</style>