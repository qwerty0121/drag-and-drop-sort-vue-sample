<script setup>
import DragAndDropSample from "@/components/DragAndDropSample.vue";
import DropArea from "./components/DropArea.vue";
</script>

<template>
  <div class="page-root">
    <DragAndDropSample />
    <DropArea />
  </div>
</template>

<style scoped>
:global(body) {
  margin: 0;
}
.page-root {
  margin: 100px;
  display: flex;
  flex-flow: row nowrap;
  justify-content: space-between;
}
</style>
