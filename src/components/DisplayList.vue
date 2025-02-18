<template>
  <div class="display-list-container" :class="listContainerStyle">
    <ul ref="childListComponent">
      <li
        v-for="(item, index) in listModel"
        :key="index"
        class="displayList-style"
        :class="listStyles"
        @click="$emit('getListId', index)"
      >
        <slot name="list-icon" :icon="item['icon']"></slot>
        <slot
          name="list-checkbox"
          :completionStatus="item['is-completed']"
          :indexVal="index"
        ></slot>
        <span>{{ item["title"] }}</span>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, defineExpose } from "vue";
const childListComponent = ref(null);
const listModel = defineModel("list-model");
const { listStyles, listContainerStyle } = defineProps({
  listStyles: String,
  listContainerStyle: String,
});
defineExpose({ childListComponent });
</script>

<style scoped>
.displayList-style {
  background-color: white;
  border-radius: 0.5rem;
  display: flex;
  align-items: baseline;
  cursor: pointer;
}
</style>
