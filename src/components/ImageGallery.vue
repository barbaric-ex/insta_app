<template>
  <div class="wrapper">
    <img
      v-for="post in props.posts"
      :key="post.id"
      :src="`${VITE_BASE_PHOTO_URL}${post.url}`"
      alt=""
    />
  </div>
</template>

<script setup>
import { defineProps } from "vue";
const { VITE_BASE_PHOTO_URL } = import.meta.env;

const props = defineProps(["posts"]);
</script>

<style scoped>
.wrapper {
  display: flex;
  justify-content: left;
  flex-wrap: wrap;
}
img {
  margin: 5px;
  width: 200px;
  object-fit: cover;
}

@media (max-width: 768px) {
  .wrapper {
    justify-content: center;
  }
}
</style>
