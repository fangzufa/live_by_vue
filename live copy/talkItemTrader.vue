<template>
  <div class="list_content_main" @click="listclick">
    <div class="list_content_trader">
      <div class="list_content_trader_top">
        <div>
          <img :src="item.icon" />
          <div>{{ item.varietyName }}</div>
        </div>

        <img
          :src="
            (item.direction-0)
              ? require('@/assets/images/live/live_call.png')
              : require('@/assets/images/live/live_put.png')
          "
        />
      </div>

      <div class="list_content_trader_link">
        {{ language["直播间"]["点击并交易"] }}
      </div>
    </div>
  </div>
</template>

<script>
import { createNamespacedHelpers } from "vuex";
const { mapState, mapMutations } = createNamespacedHelpers("bit/");

export default {
  inject: ["serverTime"],
  props: ["item"],
  data() {
    return {
      listClass: {
        1: "list_user",
        2: "list_teacher",
        3: "list_self",
        4: "list_reply",
      },
    };
  },
  computed: {
    ...mapState({
      isLogin: "isLogin",
    }),
  },
  methods: {
    ...mapMutations({
      updateTradetype: "updateTradetype",
    }),
    listclick() {
      if (!this.isLogin) {
        this.goNext("/login", { 
          tradeType: 1,
          isBack: 1
        });
        return;
      }
      if (this.isExpired(this.item.endTime)) {
        this.util.toast({ msg: this.language["直播间"]["该带单消息已失效"] });
        return;
      }
      this.updateTradetype(1);
      this.$router.push({
        path: "/trade/index",
        query: {
          varietyType: this.item.varietyType,
        },
      });
    },
    isExpired(traderTime) {
      return traderTime - this.serverTime() < 0;
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
.list_content_main {
  max-width: 84.5%;

  .list_content_trader {
    max-width: 100%;
    background: #fff;
    border-radius: 0.06rem;
    padding: 0.1rem 0.15rem;
    margin-bottom: 0.05rem;

    .list_content_trader_top {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 0.07rem 0.1rem;
      background: #e8e9ed;
      border-radius: 0.06rem;

      img {
        width: 0.26rem;
        height: 0.26rem;
        display: block;
      }
      > div {
        display: flex;
        align-items: center;

        div {
          color: #282829;
          font-size: 0.14rem;
          margin-left: 0.1rem;
        }
      }
    }

    .list_content_trader_link {
      font-size: 0.14rem;
      color: #6aadff;
      text-decoration: underline;
      margin-top: 0.07rem;
    }
  }
}
</style>
