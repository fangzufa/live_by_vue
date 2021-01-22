<template>
  <div>
    <Header :title="language['直播间']['标题']" :isBorder="!announShow"></Header>

    <section class="page_main">
      <Announcement :liveStatus="liveStatus" v-if="announShow" />
      <Broadcast from="live" />
      <section class="talk_content" ref="talk">
        <Talk :socket_311="socket_311" />
      </section>
      <TalkInput
        :socket_311="socket_311"
        @sendSocket="sendSocket"
        :liveStatus="liveStatus"
      />
    </section>

    <Carousel :list="carouselList" />
  </div>
</template>
<script>
import { createNamespacedHelpers } from "vuex";
const { mapState } = createNamespacedHelpers("bit/");

export default {
  provide: function () {
    return {
      serverTime: () => this.serverTime,
    };
  },
  data() {
    return {
      announShow: true,
      carouselList: [
        // {
        //   userName: "昵称",
        //   teacherName: "老师名称",
        //   varietyName: "品种名称",
        //   direction: "方向 1买涨0 买跌",
        // },
      ],
      liveStatus: {},
      socket_311: {},
      serverTime: 0,
      timer: null,
    };
  },
  computed: {
    ...mapState({
      isLogin: "isLogin",
      tradeType: "tradeType",
    }),
  },
  components: {
    Header: () => import("@/components/live/header"),
    Announcement: () => import("@/components/live/announcement"),
    Broadcast: () => import("@/components/trade/orderBroadcast"),
    Talk: () => import("@/components/live/talk"),
    TalkInput: () => import("@/components/live/talkInput"),
    Carousel: () => import("@/components/live/carousel"),
  },
  mounted() {
    this.sendSocket(300);
    this.getLiveStatus();
    this.getServerTime();
  },
  methods: {
    getLiveStatus() {
      this.$get("/user/live/queryLiveStatus.do").then((d) => {
        if (d.code == 200) {
          this.liveStatus = d.data;
        }
      });
    },
    getServerTime() {
      this.$get("/user/user/getSystemTime.do", {}).then((d) => {
        if (d.code == 200) {
          this.serverTime = d.data;
          clearInterval(this.timer);
          this.timer = setInterval(() => {
            this.serverTime = this.serverTime + 1000;
          }, 1000);
        }
      });
    },
    sendSocket(code, data) {
      let _this = this;

      var a = {
        code: code,
        token1: _this.isLogin ? _this.util.getCookie("token1") : "",
        token2: _this.isLogin ? _this.util.getCookie("token2") : "",
        host: _this.util.api(true),
        payType: _this.tradeType - 0,
        language: _this.language["language"],
        data: data ? data : "",
      };
      if (!this.isLogin) {
        a.deviceId = this.userdeviceId;
      }
      _webSocket.onsend(JSON.stringify(a));
      _webSocket.msg = _this.msg;
    },
    msg(event) {
      let m = JSON.parse(event),
        _this = this;
      if (m.code == 311) {
        this.socket_311 = m.data;
      } else if (m.code == 313) {
        if (m.data.self != null) {
          this.liveStatus.userStatus = m.data.self ? 1 : 0;
        }
      } else if (m.code == 319) {
        this.liveStatus.allUserTalkStatus = m.data;
      } else if (m.code == 320) {
        this.carouselList.push(JSON.parse(m.data));
      }
    },
  },
  beforeDestroy() {
    this.sendSocket(301);
    clearInterval(this.timer);
  },
};
</script>
<style scoped lang="less">
.page_main {
  padding-top: 0.45rem;
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  background: #f3f5fa;
}
.talk_content {
  flex: 1;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
}
</style>
