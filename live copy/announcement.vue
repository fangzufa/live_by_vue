<template>
  <div class="announcement">
    <div>
      {{ liveStatus.liveTitle }}
    </div>
    <img src="@/assets/images/live/live_ad_none.png" @click="none" />
  </div>
</template>

<script>
export default {
  props: ["liveStatus"],
  methods: {
    none() {
      this.$parent.announShow = false;
    },
  },
};
</script>

<style scoped lang="less">
.announcement {
  width: 100%;
  background: #fcf0d6;
  padding: 0.08rem 0.1rem 0.08rem 0.19rem;
  color: #282829;
  font-size: 0.12rem;
  display: flex;
  align-items: center;
  div {
    flex: 1;
  }
  img {
    width: 0.14rem;
    height: 0.14rem;
    margin-left: 0.05rem;
  }
}
</style>
