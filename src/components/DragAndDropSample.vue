<script setup>
import { computed, ref, toRaw } from "vue";

/** アイテムリスト */
const itemList = ref(
  [...Array(10)].map((_, i) => ({ id: i + 1, name: `name ${i + 1}` }))
);

/** 並び替え中のアイテムリスト */
const reorderingItemList = ref(null);

/** レンダリングで参照するアイテムリスト */
const itemListForRender = computed(
  () => reorderingItemList.value ?? itemList.value
);

/** アイテムリストにおけるドラッグ中のアイテムの位置 */
const draggingItemIndex = ref(null);

/**
 * アイテムをドラッグ中であるかどうかを判定するフラグ
 * ドラッグ中である場合はtrue、ドラッグ中でない場合はfalse
 */
const isDragging = computed(() => draggingItemIndex.value !== null);

function onDragStartItem(event, index) {
  event.dataTransfer.effectAllowed = "move";
  // ドラッグ中の画像を対象アイテム要素全体にする
  event.dataTransfer.setDragImage(event.target.closest(".data-item"), 20, 30);

  draggingItemIndex.value = index;
  reorderingItemList.value = structuredClone(toRaw(itemList.value));
}

function onDragEndItem(event) {
  draggingItemIndex.value = null;
  if (event.dataTransfer.dropEffect === "move") {
    itemList.value = reorderingItemList.value;
  }
  reorderingItemList.value = null;
}

function onDragEnterItem(event, index) {
  if (!isDragging.value) {
    // アイテムのドラッグ中でない場合は何もしない
    return;
  }
  if (draggingItemIndex.value === index) {
    // ドラッグ中の要素の上にドラッグしている場合は何もしない
    return;
  }

  // ドラッグ中のアイテムをリストから一時的に取り出す
  const [dragTargetItem] = reorderingItemList.value.splice(
    draggingItemIndex.value,
    1
  );
  // リスト内の現在ドラッグしている位置にドラッグ中のアイテムを挿入
  reorderingItemList.value.splice(index, 0, dragTargetItem);

  // インデックスを移動後の位置に変更
  draggingItemIndex.value = index;
}
</script>

<template>
  <div>
    <p draggable="true">アイテムリスト</p>
    <div class="data-list" @dragover.prevent @drop.prevent>
      <template v-for="(item, index) in itemListForRender" :key="item.id">
        <div
          class="data-item"
          :class="{ dragging: index === draggingItemIndex }"
          @dragenter.prevent="onDragEnterItem($event, index)"
          @dragover.prevent
        >
          <img
            src="@/assets/drag-handle.svg"
            class="drag-handle"
            draggable="true"
            @dragstart="onDragStartItem($event, index)"
            @dragend.prevent="onDragEndItem"
          />
          <div>{{ item.id }}</div>
          <div>{{ item.name }}</div>
        </div>
      </template>
    </div>
  </div>
</template>

<style scoped>
.data-list {
  width: fit-content;
  display: grid;
  grid-template-columns: auto auto auto;
  gap: 10px;
  justify-content: start;
  .drag-handle {
    width: 20px;
    height: 40px;
    cursor: grab;
  }
  .data-item {
    display: inline grid;
    grid-template-columns: subgrid;
    grid-column: span 3;
    align-items: center;
    column-gap: 20px;
    padding: 10px;
    border: 1px solid black;
    background-color: lightblue;
    &:hover:not(.dragging) {
      background-color: lightskyblue;
    }
    &.dragging {
      opacity: 0.1;
    }
  }
}
</style>
