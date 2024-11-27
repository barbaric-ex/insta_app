<template>
  <div>
    <a-button @click="showModal">Upload Photo</a-button>
    <a-modal v-model:open="open" title="Upload Photo" @ok="handleOk">
      <div class="wrap" v-if="!loading">
        <input
          type="file"
          accept="jpeg,.png,.jpg"
          @change="handleUploadChange"
        />
        <a-input
          v-model:value="caption"
          placeholder="Caption"
          :maxLength="50"
        ></a-input>
        <a-typography-text type="danger" v-if="errorMessage">{{
          errorMessage
        }}</a-typography-text>
      </div>

      <div class="sppiner" v-else>
        <a-spin></a-spin>
      </div>
    </a-modal>
  </div>
</template>
<script setup>
import { ref, defineProps } from "vue";
import { supabase } from "@/supabase";
import { useUserStore } from "../stores/users";
import { storeToRefs } from "pinia";

const userStore = useUserStore();

const props = defineProps(["addNewPost"]);
const loading = ref(false);
const errorMessage = ref("");

const { user } = storeToRefs(userStore);
const open = ref(false);
const caption = ref("");
const file = ref(null);
console.log(user.value);

const showModal = () => {
  open.value = true;
};

const handleOk = async () => {
  loading.value = true;
  const fileName = Math.floor(Math.random() * 10000000);
  let filePath;
  if (file.value) {
    const { data, error } = await supabase.storage
      .from("images")
      .upload("public/" + fileName, file.value);

    if (error) {
      loading.value = false;
      return (errorMessage.value = "Unable to upload photo");
    }

    filePath = data.path;
    const res = await supabase.from("posts").insert({
      url: filePath,
      caption: caption.value,
      owner_id: user.value.id,
    });

    console.log({ res });
  }
  loading.value = false;
  open.value = false;
  caption.value = "";

  props.addNewPost({
    url: filePath,
    caption: caption.value,
  });
};

const handleUploadChange = (e) => {
  if (e.target.files[0]) {
    file.value = e.target.files[0];
  }
};
</script>

<style scoped>
input {
  margin-top: 10px;
}

.sppiner {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
