<template>
  <ALayoutHeader>
    <Container>
      <div class="nav_wrap">
        <div class="logo">
          <router-link to="/">Instagram</router-link>
          <a-input-search
            v-model:value="searchUsername"
            placeholder="username..."
            style="width: 200px"
            @search="onSearch"
          />
        </div>

        <!-- <div class="big_wrap" v-if="!loadingUser"> -->
        <div class="buttons" v-if="!user">
          <!-- <a-button type="primary">Login </a-button> -->
          <auth-modal :isLogin="false"></auth-modal>
          <auth-modal :isLogin="true"></auth-modal>
        </div>

        <div class="buttons" v-else>
          <a-button @click="openProfile" type="primary">Profile</a-button>
          <a-button @click="handleLogout" type="primary">Logout </a-button>
        </div>
        <!-- </div> -->
      </div>
    </Container>
  </ALayoutHeader>
</template>
<script setup>
import { Layout } from "ant-design-vue";
import { RouterLink, useRouter } from "vue-router";
import Container from "./Container.vue";
import AuthModal from "./AuthModal.vue";
import { useUserStore } from "../stores/users";
import { ref } from "vue";
import { storeToRefs } from "pinia";

const userStore = useUserStore();

const { user, loadingUser } = storeToRefs(userStore);
const router = useRouter();
const isAuthenticated = ref(false);

const searchUsername = ref();

const onSearch = () => {
  if (searchUsername.value) {
    router.push(`/profile/${searchUsername.value}`);
    searchUsername.value = "";
  }
};

const openProfile = () => {
  router.push(`/profile/${user.value.username}`);
};

const handleLogout = async () => {
  await userStore.handleLogout();
};
</script>

<style scoped>
.ant-layout-header {
  background: rgb(14, 13, 13);
}

.nav_wrap {
  color: #fff;
  display: flex;
  align-items: center;
  padding: 20px 0;
  margin-right: 10px;
  justify-content: space-between;
  flex-wrap: wrap;
}

.nav_wrap button {
  margin-left: 10px;
}

.ant-layout-header .logo a {
  color: #fff;
  text-decoration: none;
  margin-right: 10px;
  font-size: 23px;
}

.buttons {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}
</style>
