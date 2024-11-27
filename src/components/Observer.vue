<template>
  <div class="observer" ref="root"></div>
</template>

<script setup>
import { ref, onMounted, defineEmits } from "vue";

const observer = ref(0);
const root = ref(null);

const emits = defineEmits(["intersecti"]);

onMounted(() => {
  observer.value = new IntersectionObserver(([entry]) => {
    if (entry && entry.isIntersecting) {
      emits("intersecti");
    }
  });

  observer.value.observe(root.value);
});
</script>

<style scoped>
.observer {
  height: 30px;
}
</style>
