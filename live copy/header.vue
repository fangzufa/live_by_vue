<template>
  <div>
    <header
      class="top_header"
      :class="{
        top_header_no_border: noBorder,
        top_header_border: isBorder,
        noBg: isNoBg,
      }"
    >
      <h3>{{ title }}</h3>

      <div class="back" @click="goback" v-if="!noLeft">
        <i class="arrow"></i>
      </div>

      <img
        class="action"
        @click="showToggleTab = true"
        src="@/assets/images/live/live_type.png"
      />
    </header>

    <ToggleTab :type="type" v-if="showToggleTab" />
  </div>
</template>

<script>
export default {
  props: ["title", "backurl", "rule", "noBorder", "noLeft", "isBorder", "isNoBg"],
  data() {
    return {
			showToggleTab: false,
      type: "",
    };
  },
  components: {
    ToggleTab: () => import("@/components/live/toggleTab"),
  },
  methods: {
    goback() {
      if (this.$route.query.isOperate == 1) {
        window.history.back();
      } else {
        if (this.backurl && this.backurl != "") {
          this.$router.push({
            path: this.backurl,
          });
        } else {
          this.$router.back();
        }
      }
    },
    callMethod() {
      this.$emit("callback");
    },
  },
};
</script>

<style scoped>
.top_header {
  align-items: center;
  background-color: #fff;
  color: #444444;
  display: flex;
  height: 0.45rem;
  justify-content: center;
  left: 0;
  position: fixed;
  top: 0;
  width: 100vw;
  z-index: 100;
}
.top_header_border {
  border-bottom: 1px solid #f3f5fa;
}
.top_header_no_border {
  border-bottom: none;
}
.noBg {
  background: #34446e;
  border-bottom: 1px solid #34446e;
}
.top_header > h3 {
  font-weight: 400;
  font-size: 0.17rem;
}
.noBg > h3 {
  color: #fff;
}
.top_header > .back {
  height: 0.44rem;
  left: 0;
  line-height: 0.44rem;
  position: absolute;
  text-align: center;
  width: 0.44rem;
}
.top_header > .back > .iconfont {
  font-size: 0.22rem;
  vertical-align: middle;
}
.top_header > .action {
  width: 0.2rem;
  height: 0.16rem;
  position: absolute;
  right: 0.15rem;
  z-index: 1;
}
.top_header > .back > .arrow {
  display: inline-block;
  -webkit-transform: rotate(225deg);
  transform: rotate(225deg);
  height: 0.12rem;
  width: 0.12rem;
  border-width: 0.02rem 0.02rem 0 0;
  border-color: #444444 !important;
  border-style: solid;
  position: absolute;
  left: 0.15rem;
  top: 0.15rem;
}
.noBg > .back > .arrow {
  border-color: #fff !important;
}
</style>
