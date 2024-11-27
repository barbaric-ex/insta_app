<template>
  <div class="wrap">
    <Card v-for="post in posts" :key="post.id" :post="post"></Card>
    <observer v-if="posts.length" @intersecti="fetchNextSet"></observer>
  </div>
</template>

<script setup>
import Card from "./Card.vue";
import Observer from "./Observer.vue";
import { supabase } from "@/supabase";
import { ref, onMounted } from "vue";
import { useUserStore } from "../stores/users";
import { storeToRefs } from "pinia";

const userStore = useUserStore();
const { user } = storeToRefs(userStore);
const posts = ref([]);
const lastcardIndex = ref(2);
const owner_ids = ref([]);
const reachEnd = ref(false);

const fatchData = async () => {
  const { data: followings } = await supabase
    .from("followers_following")
    .select("following_id")
    .eq("follower_id", user.value.id);

  owner_ids.value = followings.map((f) => f.following_id);

  const { data } = await supabase
    .from("posts")
    .select()
    .in("owner_id", owner_ids.value)
    .range(0, lastcardIndex.value)
    .order("created_at", { ascending: false });

  posts.value = data;
};

const fetchNextSet = async () => {
  if (!reachEnd.value) {
    const { data } = await supabase
      .from("posts")
      .select()
      .in("owner_id", owner_ids.value)
      .range(lastcardIndex.value + 1, lastcardIndex.value + 3)
      .order("created_at", { ascending: false });

    posts.value = [...posts.value, ...data];
    lastcardIndex.value = lastcardIndex.value + 3;

    if (!data.length) {
      reachEnd.value = true;
    }
  }
};

onMounted(() => {
  fatchData();
});
</script>

<style scoped>
.wrap {
  display: flex;
  justify-content: center;
  margin-top: 20px;
  flex-direction: column;
  align-items: center;
}
</style>
