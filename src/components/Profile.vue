<template>
  <container>
    <div class="wrap" v-if="!loading">
      <user-bar
        :key="$route.params.username"
        :user="user"
        :userInfo="userInfo"
        :addNewPost="addNewPost"
        :isFollowing="isFollowing"
        :updateIsFollowing="updateIsFollowing"
      ></user-bar>
      <image-gallery :posts="posts"></image-gallery>
    </div>

    <div class="sppiner">
      <a-spin></a-spin>
    </div>
  </container>
</template>

<script setup>
import Container from "./Container.vue";
import UserBar from "./UserBar.vue";
import ImageGallery from "./ImageGallery.vue";
import { useUserStore } from "../stores/users";
import { storeToRefs } from "pinia";
import { ref, onMounted, watch, reactive } from "vue";
import { supabase } from "@/supabase";
import { useRoute } from "vue-router";

const route = useRoute();
const user = ref(null);
const isFollowing = ref(false);
const { username } = route.params;

const userStore = useUserStore();
const { user: loggedInUser } = storeToRefs(userStore);

const posts = ref([]);
const loading = ref(false);
const userInfo = reactive({ posts: null, followers: null, folowing: null });

const addNewPost = (post) => {
  posts.value.unshift(post);
};

const updateIsFollowing = (follow) => {
  isFollowing.value = follow;
};

const fetchData = async () => {
  loading.value = true;

  const { data: userData } = await supabase
    .from("users")
    .select()
    .eq("username", username)
    .single();

  if (!userData) {
    loading.value = false;
    return (user.value = null);
  }

  user.value = userData;

  const { data: postData } = await supabase
    .from("posts")
    .select()
    .eq("owner_id", user.value.id);

  posts.value = postData;

  await fetchIsFallowing();

  const followerCount = await fetchFollowersCount();
  const followingCount = await fetchFollovingCount();

  userInfo.followers = followerCount;
  userInfo.folowing = followingCount;
  userInfo.posts = posts.value.length;

  loading.value = false;
};

const fetchFollowersCount = async () => {
  const { count } = await supabase
    .from("followers_following")
    .select("*", { count: "exact" })
    .eq("following_id", user.value.id);

  return count;
};

const fetchFollovingCount = async () => {
  const { count } = await supabase
    .from("followers_following")
    .select("*", { count: "exact" })
    .eq("follower_id", user.value.id);

  return count;
};

const fetchIsFallowing = async () => {
  if (loggedInUser.value && loggedInUser.value.id !== user.value.id) {
    const { data } = await supabase
      .from("followers_following")
      .select()
      .eq("follower_id", loggedInUser.value.id)
      .eq("following_id", user.value.id)
      .single();

    if (data) isFollowing.value = true;
  }
};

watch(loggedInUser, () => {
  fetchIsFallowing();
});

onMounted(() => {
  fetchData();
});
</script>

<style scoped>
.wrap {
  align-items: center;
}

.sppiner {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
</style>
