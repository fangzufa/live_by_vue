<template>
  <div class="order_main" @click="cancle">
    <section class="content" :class="{ show_content: conShow }" @click.stop="">
      <div class="list_title">
        {{ language["直播间"]["消息显示"] }}
      </div>

      <div class="list_main">
        <div
          v-for="item in list"
          :key="item.type"
          class="list"
          :class="{ show: type == item.type }"
          @click="toggleType(item.type)"
        >
          {{ item.text }}
        </div>
      </div>
    </section>
  </div>
</template>
<script>
import { createNamespacedHelpers } from "vuex";
const { mapState } = createNamespacedHelpers("bit/");
import liveEvent from "./liveEvent";

export default {
  props: ["type"],
  data() {
    return {
      conShow: false,
      list: [
        {
          text: this.language["直播间"]["只看老师"],
          type: 1,
        },
        {
          text: this.language["直播间"]["全部"],
          type: "",
        },
        // {
        //   text: this.language["直播间"]["交易信息"],
        //   type: 3,
        // },
      ],
    };
  },
  computed: {
    ...mapState({
      isLogin: "isLogin",
    }),
  },
  mounted() {
    setTimeout(() => {
      this.conShow = true;
    }, 100);
  },
  methods: {
    toggleType(type) {
      // if (type == 3) {
      //   if (!this.isLogin) {
      //     this.goNext("/login", {
      //       tradeType: 1,
      //       isBack: 1,
      //     });
      //     return;
      //   }
      //   this.goNext("/trade/index");
      //   this.cancle();
      //   return;
      // }
      liveEvent.$emit("getTaskList", type, true);
      this.$parent.type = type;
      this.cancle();
    },
    cancle() {
      this.conShow = false;
      setTimeout(() => {
        this.$parent.showToggleTab = false;
      }, 200);
    },
    goNext(name, obj) {
      this.$router.push({
        path: name,
        query: obj || {},
      });
    },
  },
};
</script>
<style scoped lang="less">
.order_main {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.4);
  z-index: 1000;
  .content {
    width: 100vw;
    background-color: #fff;
    position: absolute;
    bottom: -60vh;
    left: 0;
    transition: all 0.2s ease;
    border-radius: 30px 30px 0px 0px;
    overflow: hidden;
    .list_title {
      color: #999999;
      font-size: 0.14rem;
      font-weight: 400;
      text-align: center;
      padding: .28rem 0 0.1rem;
    }
    .list_main {
      display: flex;
      flex-direction: column;
      .list {
        width: 100%;
        text-align: center;
        height: 0.5rem;
        line-height: 0.5rem;
        font-size: 0.18rem;
        color: #000000;
        &.show {
          background: #6aadff;
          color: #fff;
        }
      }
    }
  }
  .show_content {
    bottom: 0;
  }
}

/* iphoneX、iphoneXs */
@media only screen and (device-width: 375px) and (device-height: 812px) and (-webkit-device-pixel-ratio: 3) {
  .content {
    padding-bottom: 0.34rem;
  }
}

/* iphone Xs Max */
@media only screen and (device-width: 414px) and (device-height: 896px) and (-webkit-device-pixel-ratio: 3) {
  .content {
    padding-bottom: 0.34rem;
  }
}

/* iphone XR */
@media only screen and (device-width: 414px) and (device-height: 896px) and (-webkit-device-pixel-ratio: 2) {
  .content {
    padding-bottom: 0.34rem;
  }
}
</style>
