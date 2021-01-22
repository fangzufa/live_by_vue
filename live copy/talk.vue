<template>
  <div class="main" ref="talk">
    <ul class="list_main">
      <li class="list_top">- - End - -</li>
      <li v-for="item in list" :key="item.id">
        <TalkItem :item="item" v-if="item.shieldStatus == 1 ? item.self : true" />
      </li>
      <li class="list_bottom" ref="bottom"></li>
    </ul>
    <div class="loading" v-show="!firstInit">
      <span></span>
      <span></span>
      <span></span>
    </div>
  </div>
</template>

<script>
import liveEvent from "./liveEvent";

export default {
  props: ["socket_311"],
  data() {
    return {
      taskType: "",
      list: [
        // {
        //   id: 101,
        //   userName: "nihao",
        //   userPortrait: "",
        //   userType: 0,
        //   content: "nihao",
        //   shieldStatus: 0,
        //   talkType: 0,
        //   createTime: "52167351213123",
        //   question: "问题内容",
        //   questionId: "问题id",
        //   questionName: "提问题的人的昵称",
        //   rate: "老师的准确率",
        // },
      ],
      inDown: true,
      firstInit: false,
      oTalk: null,
    };
  },
  components: {
    TalkItem: () => import("@/components/live/talkItem"),
  },
  watch: {
    socket_311(val) {
      if (val) {
        this.list.push(val);
        if (this.inDown || val.self) {
          this.inDown = true;
          this.handleScrollDown();
        }
      }
    },
  },
  mounted() {
    this.getTaskList();
    liveEvent.$on("getTaskList", this.getTaskList);
    this.$refs.talk.addEventListener("scroll", this.handleScroll);
    this.oTalk = this.$refs.talk;
    window.onresize = this.handleScrollDown;
  },
  methods: {
    getTaskList(type = this.taskType, isToggle = false) {
      if (isToggle) {
        this.inDown = isToggle;
      }
      let json = {
        type: type,
        page: 0,
        pageSize: 200,
      };
      if (json.type == "") {
        delete json.type;
      }
      this.$get("/user/live/initLiveComments.do", json).then((d) => {
        if (d.code == 200) {
          this.list = d.data.reverse();
          this.handleScrollDown();
        }
      });
    },
    handleScroll(event) {
      if (
        this.oTalk.scrollHeight - this.oTalk.clientHeight - this.oTalk.scrollTop >
        150
      ) {
        this.inDown = false;
      } else {
        this.inDown = true;
      }
    },
    handleScrollDown() {
      if (!this.inDown) {
        this.firstInit = true;
        return false;
      }
      this.$nextTick(() => {
        setTimeout(() => {
          this.firstInit = true;
          this.inDown = true;
          this.$refs.bottom.scrollIntoView();
        }, 1000);
      });
    },
  },
  beforeDestroy() {
    liveEvent.$off("getTaskList", this.getTaskList);
    this.$refs.talk.removeEventListener("scroll", this.handleScroll);
    window.onresize = null;
  },
};
</script>

<style scoped lang="less">
.main {
  width: 100%;
  height: 100%;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
  position: relative;
}
.list_main {
  padding: 0 0.15rem;
}
.list_top {
  font-size: 0.12rem;
  color: #636d87;
  text-align: center;
  margin: 0.15rem 0;
}
.list_bottom {
  height: 0;
  overflow: hidden;
}
.loading {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f3f5fa;
  position: absolute;
  left: 0;
  top: 0;
}
.loading span {
  display: block;
  width: 0.04rem;
  height: 0.16rem;
  border-radius: 0.04rem;
  background: #ef3024;
  animation: load 1s ease infinite;
  margin: 0 0.02rem;
}
@keyframes load {
  0%,
  100% {
    height: 0.16rem;
  }
  50% {
    height: 0.22rem;
    margin: -0.03rem 0.02rem;
  }
}
.loading span:nth-child(2) {
  animation-delay: 0.2s;
}
.loading span:nth-child(3) {
  animation-delay: 0.4s;
}
</style>
