<template>
  <div class="container">
    <van-pull-refresh v-model="refreshing" @refresh="onRefresh">
      <van-list
        offset="0"
        v-model:loading="loading"
        :finished="finished"
        finished-text="没有更多了"
        @load="onLoad"
      >
        <van-cell v-for="(item, index) in list" :key="index" :title="index" />
      </van-list>
    </van-pull-refresh>
  </div>
</template>
<script >
import { PullRefresh, List, Cell } from "vant";
import { onMounted, ref } from "vue";

export default {
  components: {
    [PullRefresh.name]: PullRefresh,
    [List.name]: List,
    [Cell.name]: Cell,
  },
  setup() {
    const list = ref([]);
    const loading = ref(false);
    const finished = ref(false);
    const refreshing = ref(false);
    const pageNum = ref(0);

    const queryRecords = () => {
      const url = "/api/digital-medical/growth-record/page-history?pageNum=" + pageNum.value;
      return fetch(url, { method: "get"})
        .then((res) => res.json())
        .then((res) => {
          const { list } = res.data;
          pageNum.value++;
          return list;
        });
    };

    const onLoad = async () => {
      loading.value = true;

      list.value = [...list.value, ...(await queryRecords())];

      loading.value = false;

    };

    const onRefresh = () => {
      // 清空列表数据
      finished.value = false;

      // 重新加载数据
      // 将 loading 设置为 true，表示处于加载状态
      loading.value = true;
      onLoad();
    };

    return {
      list,
      onLoad,
      loading,
      finished,
      onRefresh,
      refreshing,
    };
  },
};
</script>

<style lang="less">
* {
  margin: 0;
  padding: 0;
}
.container {
  height: 100vh;
}
</style>
