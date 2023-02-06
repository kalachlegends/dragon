<script setup>
import Drag from "./components/Drag.vue";
import { ref } from "vue";
</script>

<template>
  <Drag> "govmp" </Drag>
</template>

<style scoped>
body {
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 1200px;
  height: 1200px;
}
#app {
  width: 1200px;
  height: 1200px;
}
</style>
