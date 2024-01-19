<template>
  <div id="infinite-scroll">
    <ol>
      <li v-for="item in items" :key="item.id">{{ item.title }}</li>
    </ol>

    <div v-if="loading">讀取中...</div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import axios from "axios";

const items = ref([]);
const loading = ref(false);
const itemCount = ref(1);

const loadMore = async () => {
  if (loading.value) return;

  loading.value = true;
  try {
    const response = await axios.get(
      `https://api.github.com/repos/octocat/Spoon-Knife/issues?page=${itemCount.value}`
    );
    if (itemCount.value === 1) {
      items.value = items.value.concat(response.data);
      itemCount.value += 1;
    } else {
      items.value = items.value.concat(response.data.slice(0, 10));
      itemCount.value += 1;
    }
  } catch (error) {
    console.error("GET API ERROR", error);
  }
  loading.value = false;
};

const handleScroll = () => {
  const { scrollTop, scrollHeight, clientHeight } = document.documentElement;

  if (clientHeight + scrollTop >= scrollHeight - 5) {
    loadMore();
  }
};

onMounted(() => {
  loadMore();
  window.addEventListener("scroll", handleScroll);
});

onUnmounted(() => {
  window.removeEventListener("scroll", handleScroll);
});
</script>

<style lang="scss" scoped></style>
