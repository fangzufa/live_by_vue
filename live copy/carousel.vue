<template>
  <div>
    <div
      class="list"
      :class="item.direction - 0 ? 'green' : 'red'"
      v-for="(item, index) in showList"
      :key="item.id"
      :style="`animation-delay:${index * 1.1}s`"
    >
      {{ item.userName }} {{ language["直播间"]["跟随"] }} {{ item.teacherName }}
      {{ language["直播间"]["和"] }}
      {{ item.direction - 0 ? language["直播间"]["买入"] : language["直播间"]["卖出"] }}
      {{ item.varietyName }}
    </div>
  </div>
</template>

<script>
export default {
  props: ["list"],
  data() {
    return {
      timer: null,
      showList: [],
      deleteListLength: 0,
    };
  },
  watch: {
    list(val) {
      if (val) {
        this.showList = val.slice(this.deleteListLength);
      }
    },
    showList(val) {
      if (this.timer) {
        clearTimeout(this.timer);
      }
      this.timer = setTimeout(() => {
        this.deleteListLength = this.showList.length;
      }, 4000);
    },
  },
};
</script>

<style scoped lang="less">
.list {
  position: fixed;
  bottom: 1.2rem;
  color: #282829;
  font-size: 0.1rem;
  padding: 0.06rem 0.1rem;
  border-radius: 100px;
  left: -4rem;
  animation: carousel 2s ease;
  animation-iteration-count: 1;
  animation-fill-mode: forwards;
}
.green {
  background: rgba(20, 212, 144, 0.8);
}
.red {
  background: rgba(239, 48, 36, 0.7);
  color: #fff;
}

@keyframes carousel {
  0% {
    left: -4rem;
  }
  35% {
    left: 0.14rem;
    bottom: 1.2rem;
    opacity: 1;
  }
  55% {
    left: 0.14rem;
    bottom: 1.2rem;
    opacity: 1;
  }
  99% {
    left: 0.14rem;
    bottom: 1.68rem;
    opacity: 0;
  }
  100% {
    left: 0.14rem;
    opacity: 0;
    visibility: hidden;
  }
}
</style>
