<template>
  <div class="list" :class="{ list_self: item.self }">
    <div class="user_img" v-if="!item.self">
      <img
        class="user_img_img"
        :src="
          item.userPortrait
            ? item.userPortrait
            : require('@/assets/images/user_default.png')
        "
      />
    </div>

    <div class="list_content_dom">
      <div class="user_name" v-if="!item.self">
        <div>
          {{ item.userName }}
          <span class="user_name_span user_name_teacher" v-if="item.userType == 0">
            {{ language["直播间"]["老师"] }}
          </span>
        </div>

        <div class="teacher_accuracy" v-if="item.talkType == 1">
          {{ language["直播间"]["准确率"] }}：{{ item.rate }}%
        </div>
      </div>

      <div class="list_content_main" v-if="item.talkType != 1">
        <div class="list_content">
          <pre>{{ item.content }}</pre>
        </div>

        <div v-if="item.talkType == 2" class="list_content_reply">
          <pre>{{ item.questionName }}: {{ item.question }}</pre>
        </div>
      </div>
      <TalkItemTrader v-else :item="JSON.parse(item.content)" />

      <div class="list_time">
        {{ util.format(item.createTime, "h:m") }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["item"],
  data() {
    return {};
  },
  components: {
    TalkItemTrader: () => import("@/components/live/talkItemTrader"),
  },
};
</script>

<style scoped lang="less">
.list {
  width: 100%;
  display: flex;
  margin: 0.1rem 0;
}
.list_content_dom {
  flex: 1;
}
.user_img {
  .user_img_img {
    width: 0.4rem;
    height: 0.4rem;
    border-radius: 50%;
    margin-right: 0.1rem;
  }
}

.user_name {
  max-width: 84.5%;
  color: #282829;
  font-size: 0.12rem;
  height: 0.2rem;
  line-height: 0.2rem;
  display: flex;
  justify-content: space-between;

  .user_name_span {
    padding: 0 0.1rem;
    border-radius: 100px;
    &.user_name_teacher {
      background: #6aadff;
      color: #f8f8f8;
    }
  }
}
.list_content_main {
  max-width: 84.5%;
  pre {
    display: inline-block;
  }
  .list_content pre {
    max-width: 100%;
    min-height: 0.34rem;
    background: #fff;
    border-radius: 0.06rem;
    padding: 0.1rem 0.15rem;
    margin: 0.05rem 0;
    font-size: 0.14rem;
  }
  .list_content_reply pre {
    min-height: 0.22rem;
    line-height: 0.22rem;
    padding: 0 0.07rem;
    font-size: 0.1rem;
    background: #d9dbe0;
    border-radius: 0.03rem;
    color: #838383;
    margin-bottom: 0.05rem;
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-inline-box;
    -webkit-line-clamp: 1;
    -webkit-box-orient: vertical;
  }
}
.list_time {
  color: #c7c7c7;
  font-size: 0.12rem;
}

.list_self {
  .list_content_dom {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
  }
  pre {
    background: #6e7d9a !important;
    color: #ffffff !important;
  }
}
</style>
