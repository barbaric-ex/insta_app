<template>
  <div class="wrapper" v-if="props.user">
    <div class="top_content">
      <a-typography-title :level="2">{{
        props.user.username
      }}</a-typography-title>
      <div class="wrapper_btn" v-if="user">
        <upload-photo-modal
          v-if="profileUsermane === user.username"
          :addNewPost="addNewPost"
        ></upload-photo-modal>
        <div class="wrapp_fol" v-else>
          <a-button v-if="props.isFollowing" @click="unFollowUser"
            >Following</a-button
          >
          <a-button v-else @click="followUser">Follow</a-button>
        </div>
      </div>
    </div>

    <div class="bottom_content">
      <a-typography-title :level="5"
        >{{ props.userInfo.posts }} posts</a-typography-title
      >
      <a-typography-title :level="5"
        >{{ props.userInfo.followers }} followers</a-typography-title
      >
      <a-typography-title :level="5"
        >{{ props.userInfo.folowing }} following</a-typography-title
      >
    </div>
  </div>

  <div class="wrapper" v-else>
    <div class="top_content">
      <a-typography-title :level="2">User not found</a-typography-title>
    </div>
  </div>
</template>

<script setup>
import { defineProps } from "vue";
import UploadPhotoModal from "./UploadPhotoModal.vue";
import { useRoute } from "vue-router";
import { useUserStore } from "../stores/users";
import { storeToRefs } from "pinia";
import { supabase } from "@/supabase";

const userStore = useUserStore();

const route = useRoute();

const { user } = storeToRefs(userStore);

const { username: profileUsermane } = route.params;

const props = defineProps([
  "user",
  "userInfo",
  "addNewPost",
  "isFollowing",
  "updateIsFollowing",
]);

const followUser = async () => {
  props.updateIsFollowing(true);
  await supabase.from("followers_following").insert({
    follower_id: user.value.id,
    following_id: props.user.id,
  });
};

const unFollowUser = async () => {
  props.updateIsFollowing(false);
  await supabase
    .from("followers_following")
    .delete()
    .eq("follower_id", user.value.id)
    .eq("following_id", props.user.id);
};
</script>

<style scoped>
.wrapper {
  padding-bottom: 75px;
}

.wrapper .bottom_content {
  display: flex;
  width: 100%;
  align-items: center;
}

.wrapper .bottom_content h5 {
  margin: 0 !important;
  padding: 0;
  margin-right: 30px !important;
}

.top_content {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
