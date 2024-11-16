<template>
  <div class="drop-area" @dragover.prevent>ドロップエリア</div>
</template>

<style scoped>
.drop-area {
  min-height: 200px;
  min-width: 200px;
  border: 1px solid black;
  display: grid;
  place-items: center;
}
</style>
