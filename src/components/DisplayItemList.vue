<template>
  <div class="display-list-container" :class="listContainerStyle">
    <ul ref="childListComponent">
      <li
        v-for="(item, index) in listModel"
        :key="index"
        class="displayList-style"
        :class="listStyles"
        @click="handleListClick(index, $event)"
      >
        <slot name="list-icon" :icon="item['icon']"></slot>
        <slot
          name="list-checkbox"
          :completionStatus="item['is-completed']"
          :indexVal="index"
        ></slot>
        <span
          ref="listTitle"
          :data-key="index"
          class="list-name"
          @dblclick="changeTitle(index)"
          >{{ item["title"] }}</span
        >
        <slot name="list-count" :count="item['task-count']"></slot>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, defineExpose } from "vue";
const childListComponent = ref(null);
const listTitle = ref(null);
let isCheckbox = ref(false);
const listModel = defineModel("list-model");
const emit = defineEmits(["getListId", "getTitleKey"]);

const changeTitle = (index) => {
  emit("getTitleKey", index);
};

const { listStyles, listContainerStyle } = defineProps({
  listStyles: String,
  listContainerStyle: String,
});
defineExpose({ childListComponent });

const handleListClick = (index, event) => {
  if (event?.target?.type === "checkbox") {
    isCheckbox.value = true;
  } else {
    isCheckbox.value = false;
  }
  emit("getListId", index, isCheckbox.value);
};
</script>

<style scoped>
.displayList-style {
  background-color: white;
  border-radius: 0.5rem;
  display: flex;
  align-items: baseline;
  cursor: pointer;
}
.list-name {
  user-select: none;
  padding-right: 0.5rem;
}
</style>
