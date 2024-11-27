<template>
  <container>
    <div class="big_wrap" v-if="!loadingUser">
      <cards v-if="user"></cards>
      <timeline-no-posts v-else></timeline-no-posts>
    </div>

    <div v-else class="spinner">
      <a-spin></a-spin>
    </div>
  </container>
</template>

<script setup>
import Container from "./Container.vue";
import { useUserStore } from "../stores/users";
import { storeToRefs } from "pinia";
import Cards from "./Cards.vue";
import TimelineNoPosts from "./TimelineNoPosts.vue";

const userStore = useUserStore();

const { user, loadingUser } = storeToRefs(userStore);
</script>

<style scoped></style>
