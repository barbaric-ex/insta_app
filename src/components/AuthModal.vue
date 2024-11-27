<template>
  <div>
    <a-button type="primary" @click="showModal">{{ title }}</a-button>

    <a-modal v-model:open="open" :title="title" @ok="handleOk">
      <template #footer>
        <a-button key="back" @click="handleCancel">Cancle</a-button>
        <a-button
          :disabled="loading"
          key="submit"
          type="primary"
          :loading="loading"
          @click="handleOk"
          >Submit</a-button
        >
      </template>

      <div v-if="!loading" class="input_wrap">
        <a-input
          v-if="!isLogin"
          v-model:value="userCredidentals.username"
          placeholder="Username"
        />
        <a-input
          class="inp2"
          v-model:value="userCredidentals.email"
          placeholder="Email"
        />
        <a-input
          v-model:value="userCredidentals.password"
          placeholder="Password"
          type="password"
        />
      </div>

      <div v-else class="spinner_wrap">
        <a-spin></a-spin>
      </div>

      <a-typography-text type="danger" v-if="errorMessage">{{
        errorMessage
      }}</a-typography-text>
    </a-modal>
  </div>
</template>

<script lang="ts" setup>
import { ref, defineProps, reactive } from "vue";
import { useUserStore } from "../stores/users";
import { storeToRefs } from "pinia";

const userStore = useUserStore();
const { errorMessage, loading, user } = storeToRefs(userStore);
const userCredidentals = reactive({
  email: "",
  password: "",
  username: "",
});

const props = defineProps(["isLogin"]);
const title = props.isLogin ? "Login" : "Sign in";

const open = ref(false);

const showModal = () => {
  open.value = true;
  userStore.getUser();
};

const clearUserInputs = () => {
  userCredidentals.password = "";
  userCredidentals.email = "";
  userCredidentals.username = "";
  userStore.clearErrorMessage();
};

const handleOk = async (e) => {
  if (props.isLogin) {
    await userStore.handleLogin({
      email: userCredidentals.email,

      password: userCredidentals.password,
    });
  } else {
    await userStore.handleSignup(userCredidentals);
  }
  if (user.value) {
    open.value = false;
    clearUserInputs();
  }
};

const handleCancel = () => {
  clearUserInputs();
  open.value = false;
};
</script>

<style scoped>
.input_wrap {
  height: 120px;
}

.spinner_wrap {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 120px;
}

.ant-modal-content {
}
.ant-modal-content input.inp2 {
  margin: 10px 0;
}
</style>
