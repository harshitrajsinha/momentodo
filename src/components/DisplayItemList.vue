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
          @dblclick="changeTitle(index, $event)"
          >{{ item["title"] }}</span
        >
        <slot name="list-count" :count="item['task-count']"></slot>
        <div class="options-container" @click="handleOptions($event)">
          <div :style="{ transform: `rotate(90deg)` }" class="options-button">
            ...
          </div>
          <div class="options" :data-key="index">
            <div
              class="opt-edit"
              :data-key="index"
              @click="changeTitle(index, $event)"
            >
              Edit
            </div>
            <div
              class="opt-delete"
              :data-key="index"
              @click="handleListDelete(index, $event)"
            >
              Delete
            </div>
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, defineExpose } from "vue";
const childListComponent = ref(null);
const listTitle = ref(null);
const listModel = defineModel("list-model");
const emit = defineEmits(["getListId", "getTitleKey", "deleteList"]);

const changeTitle = (index, event) => {
  event.stopPropagation();
  if (event?.target?.className === "opt-edit") {
    handleOptions(event);
  }
  emit("getTitleKey", index);
};

const { listStyles, listContainerStyle } = defineProps({
  listStyles: String,
  listContainerStyle: String,
});
defineExpose({ childListComponent });

const handleListClick = (index, event) => {
  emit("getListId", index, event);
};

const handleOptions = (event) => {
  let options;
  if (event?.target?.className === "options-container") {
    options = event.target.lastElementChild;
  } else if (event?.target?.className === "options-button") {
    options = event.target.nextElementSibling;
  } else if (event?.target?.parentElement?.className === "options") {
    options = event.target.parentElement;
  }
  if (options.style.display === "none" || options.style.display === "") {
    options.style.display = "block";
  } else if (options.style.display === "block") {
    options.style.display = "none";
  }
};

const handleListDelete = (index, event) => {
  event.stopPropagation();
  if (event?.target?.className === "opt-delete") {
    handleOptions(event);
  }
  emit("deleteList", index);
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
  flex-grow: 1;
}
.options-container {
  background-color: #efecec;
  padding: 0rem 0.1rem 0rem 0.6rem;
  border-radius: 0.4rem;
  cursor: pointer;
  transform: scale(1.2);
  position: relative;
}
.options-button {
  color: #646262;
}
.options {
  z-index: 10;
  display: none;
  position: absolute;
  background: #e9e8e8;
  top: 3px;
  border-radius: 0.2rem;
  right: 23px;
  font-size: 0.8rem;
}
.options div {
  border-bottom: 1px solid #0000002b;
  padding: 0.5rem 3rem 0.5rem 0.5rem;
}
.options div:hover {
  background-color: #dcdbdb;
}
</style>
