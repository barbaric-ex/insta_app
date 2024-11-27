<template>
  <profile :key="$route.params.username"></profile>
</template>

<script setup>
import Profile from "@/components/Profile.vue";
</script>
