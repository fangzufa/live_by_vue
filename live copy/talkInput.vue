<template>
  <div class="main">
    <img
      class="tab"
      src="@/assets/images/live/live_trade.png"
      @click="goNext('/trade/index')"
    />
    <section v-if="!banInput" @click="userInput" class="input_main">
      <input
        class="input"
        type="text"
        :placeholder="language['直播间']['请输入']"
        maxlength="100"
        v-model.trim="text"
        :disabled="liveStatus.userStatus == 2 ? true : false"
        v-blur
        id="input"
      />
      <label for="input">
        <div class="send" @click.stop="sendTalk" v-show="sendShow">
          {{ language["直播间"]["发送"] }}
        </div>
      </label>
    </section>

    <div v-else class="prohibit_input">
      {{ banInputText }}
    </div>
  </div>
</template>

<script>
import { createNamespacedHelpers } from "vuex";
const { mapState, mapMutations } = createNamespacedHelpers("bit/");
import MessageBox from "@/assets/js/message-box.js";

export default {
  props: ["liveStatus", "socket_311"],
  data() {
    return {
      text: "",
      sendShow: false,
      banInput: false,
      banInputText: this.language["直播间"]["全部禁言"],
      sendOnoff: false,
    };
  },
  watch: {
    text(val) {
      this.sendShow = val !== "";
    },
    socket_311(val) {
      if (val && val.self) {
        this.text = "";
        this.sendOnoff = false;
      }
    },
    liveStatus: {
      handler: function (val, oldVal) {
        if (val) {
          this.liveStatusInit();
        }
      },
      deep: true,
    },
  },
  computed: {
    ...mapState({
      isLogin: "isLogin",
    }),
  },
  mounted() {
    this.liveStatusInit();
  },
  methods: {
    ...mapMutations({
      updateTradetype: "updateTradetype",
    }),
    liveStatusInit() {
      if (this.liveStatus.allUserTalkStatus == 1 || this.liveStatus.userStatus == 1) {
        this.banInput = true;
        if (this.liveStatus.allUserTalkStatus == 0 && this.liveStatus.userStatus == 1) {
          this.banInputText = this.language["直播间"]["你被禁言"];
        }
      } else {
        this.banInput = false;
      }
    },
    userInput() {
      if (!this.isLogin) {
        this.goNext("/login", {
          tradeType: 1,
          isBack: 1,
        });
        return;
      }
      if (this.liveStatus.userStatus == 2) {
        MessageBox({
          title: this.language["提示"],
          message: this.language["直播间"]["请先充值交易"],
          confirmButtonText: this.language["二元期权"]["充币"],
          cancelButtonText: this.language["取消"],
          showCancelButton: true,
        }).then((action) => {
          if (action == "confirm") {
            this.updateTradetype(1);
            this.$parent.$parent.toDeposit("/recharge/deposit?from=index");
          }
        });
        return;
      }
    },
    sendTalk() {
      if (this.text.length > 100) {
        this.util.toast({ msg: this.language["直播间"]["最大输入100个字符"] });
        return;
      }
      if (this.sendOnoff) {
        return;
      }
      this.sendOnoff = true;
      this.$emit("sendSocket", 302, this.text);
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
.main {
  width: 100vw;
  height: 0.6rem;
  background: #fff;
  display: flex;
  align-items: center;
}
.tab {
  width: 0.2rem;
  height: 0.2rem;
  display: block;
  margin: 0 0.2rem;
}
.input_main {
  flex: 1;
  display: flex;
}
.input {
  width: 0;
  flex: 1;
  font-size: 0.16rem;
  &::placeholder {
    color: #999999;
    font-size: 0.16rem;
  }
}
input[disabled],
input:disabled,
input.disabled {
  background: #fff;
  opacity: 1;
}
.send {
  min-width: 0.8rem;
  height: 0.4rem;
  line-height: 0.4rem;
  padding: 0 0.2rem;
  background: #6aadff;
  font-size: 0.16rem;
  color: #ffffff;
  margin: 0 0.15rem;
  border-radius: 0.05rem;
}
.prohibit_input {
  color: #000000;
  font-size: 0.16rem;
  flex: 1;
  border: 1px dashed #cacaca;
  margin-right: 0.57rem;
  text-align: center;
  height: 0.4rem;
  line-height: 0.4rem;
  border-radius: 0.03rem;
}

/* iphoneX、iphoneXs */
@media only screen and (device-width: 375px) and (device-height: 812px) and (-webkit-device-pixel-ratio: 3) {
  /*导航操作栏上移*/
  .main {
    height: 0.94rem;
    padding-bottom: 0.34rem;
  }
}

/* iphone Xs Max */
@media only screen and (device-width: 414px) and (device-height: 896px) and (-webkit-device-pixel-ratio: 3) {
  /*导航操作栏上移*/
  .main {
    height: 0.94rem;
    padding-bottom: 0.34rem;
  }
}

/* iphone XR */
@media only screen and (device-width: 414px) and (device-height: 896px) and (-webkit-device-pixel-ratio: 2) {
  /*导航操作栏上移*/
  .main {
    height: 0.94rem;
    padding-bottom: 0.34rem;
  }
}
</style>
