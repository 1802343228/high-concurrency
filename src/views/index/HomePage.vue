<template>
  <v-app style="background-color: #f3f3f3">
    <nav-bar></nav-bar>
    <div>
      <div class="solid">
        <v-card flat tile>
          <v-window v-model="onboarding" reverse>
            <v-window-item v-for="n in length" :key="`card-${n}`">
              <v-card color="teal lighten-1" height="300">
                <v-row class="fill-height" align="center" justify="center">
                  <h1 style="font-size: 5rem" class="white--text">
                    Slide {{ n }}
                  </h1>
                </v-row>
              </v-card>
            </v-window-item>
          </v-window>

          <v-card-actions class="justify-space-between">
            <v-btn text @click="prev">
              <img src="../../assets/icon/hou.png"/>
            </v-btn>
            <v-item-group v-model="onboarding" class="text-center" mandatory>
              <v-item
                v-for="n in length"
                :key="`btn-${n}`"
                v-slot="{ active, toggle }"
              >
                <v-btn :input-value="active" icon @click="toggle">
                  <img src="../../assets/icon/dain.png"/>
                </v-btn>
              </v-item>
            </v-item-group>
            <v-btn text @click="next">
              <img style="width:30px;height30px;" src="../../assets/icon/prev.png"/>
            </v-btn>
          </v-card-actions>
        </v-card>
      </div>

      <div class="miaosha">
        <div class="miaosha-border">
          <h1 style="margin-top: 20px">骸冰秒杀</h1>
          <img class="dian" src="../../assets/icon/dian.png" />
          <div class="daojishi">
            <p class="time-border margin">倒</p>
            <p class="margin">:</p>
            <p class="time-border margin">计</p>
            <p class="margin">:</p>
            <p class="time-border margin">时</p>
          </div>
        </div>

        <div>
          <v-sheet class="mx-auto" elevation="8" width="880">
            <v-slide-group
              v-model="model"
              class="pa-4"
              active-class="success"
              show-arrows
              prev-icon="«"
              next-icon="»"
            >
              <v-slide-item v-for="(item, index) in goods" :key="index">
                <v-card
                  class="ma-1"
                  height="370"
                  width="250"
                  @click="goGoods(item)"
                >
                  <img class="goodsImg" :src="item.image" />

                  <v-card-title
                    >{{ item.description }} {{ item.goodName }}</v-card-title
                  >

                  <v-card-text>
                    <v-row align="center" class="mx-0"> </v-row>

                    <div class="my-4 subtitle-1">
                      <span class="type">{{ item.type }}</span>
                      <span> ${{ item.price }}</span>
                    </div>
                  </v-card-text>
                </v-card>
              </v-slide-item>
            </v-slide-group>
          </v-sheet>
        </div>
      </div>
    </div>
  </v-app>
</template>


<script>
import NavBar from "../../components/NavBar";
export default {
  name: "HomePage",
  data() {
    return {
      length: 3,
      onboarding: 0,
      model: null,
      goods: [],
    };
  },
  mounted: function () {
    var params = new URLSearchParams();
    params.append("currentPage", "1");
    params.append("pageSize", "10");
    this.axios
      .post(this.GLOBAL.contentUrl + "/goods/findAllGoods", params)
      .then((res) => {
        this.goods = res.data.data.Goods.content;
        console.log(this.goods);
      });
  },
  methods: {
    next() {
      this.onboarding =
        this.onboarding + 1 === this.length ? 0 : this.onboarding + 1;
    },
    prev() {
      this.onboarding =
        this.onboarding - 1 < 0 ? this.length - 1 : this.onboarding - 1;
    },
    goGoods(goods) {
      this.$router.push({ path: "/goods", query: { goodsInfo: goods } });
    },
  },
  components: {
    NavBar,
  },
};
</script>
<style lang="scss" scoped>
.solid {
  width: 85%;
  margin: auto;
}
.daojishi {
  display: flex;
}
.miaosha {
  width: 85%;
  margin: auto;
  display: flex;
  margin-top: 10px;
}
.miaosha-border {
  height: 400px;
  width: 300px;
  background-color: #26a69a;
  text-align: center;
}
.daojishi {
  border: 100px;
  margin: 120px 50px 50px 50px;
}
.margin {
  margin: 10px;
}
.time-border {
  width: 50px;
  height: 30px;
  background: #ffffff;
}
.dian {
  width: 50px;
  height: 50px;
  margin-top: 80px;
}
.goodsImg {
  width: 250px;
  height: 240px;
}
.type {
  background-color: #26a69a;
  color: #ffffff;
  padding: 5px;
}
</style>
