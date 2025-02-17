<template>
  <div class="display-list-container" :class="props.listContainerStyle">
    <ul ref="childListComponent">
      <li
        v-for="(item, index) in listModel"
        :key="index"
        class="displayList-style"
        :class="props.listStyles"
        @click="handleListClick(index)"
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
import { defineProps, ref, defineEmits, defineExpose } from "vue";
const childListComponent = ref(null);
const listModel = defineModel("list-model");
const props = defineProps(["listStyles", "listContainerStyle"]);
const emit = defineEmits("getListId");

const handleListClick = (index) => {
  emit("getListId", index);
};

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
