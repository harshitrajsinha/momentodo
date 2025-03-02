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
          @dblclick="handleListUpdate(index, $event)"
          >{{ item["title"] }}</span
        >
        <slot name="list-count" :count="item['task-count']"></slot>
        <div
          ref="optionsContainerElem"
          class="options-container"
          :data-key="index"
          @click="handleOptions($event)"
        >
          <div :style="{ transform: `rotate(90deg)` }" class="options-button">
            ...
          </div>
          <div class="options" :data-key="index">
            <div
              class="opt-edit"
              :data-key="index"
              @click="handleListUpdate(index, $event)"
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
import { ref } from "vue";
let optionsElem = ref({});
let optionContainerActive = ref(false);
const optionsContainerElem = ref(null);
const listTitle = ref(null);
const listModel = defineModel("list-model");
const emit = defineEmits({
  "get-list-id": (index, event) => {
    if (typeof index === "number" && typeof event === "object") {
      return true;
    } else {
      console.warn("Invalid List ID");
      return false;
    }
  },
  "update-list": (index) => {
    if (typeof index === "number") {
      return true;
    } else {
      console.warn("Invalid List ID");
      return false;
    }
  },
  "delete-list": (index) => {
    if (typeof index === "number") {
      return true;
    } else {
      console.warn("Invalid List ID");
      return false;
    }
  },
});

const { listStyles, listContainerStyle } = defineProps({
  listStyles: String,
  listContainerStyle: String,
});

const handleListClick = (index, event) => {
  emit("get-list-id", index, event);
};

const handleOptions = (event) => {
  let elemKey =
    event?.target?.getAttribute("data-key") ||
    event?.target?.parentElement?.getAttribute("data-key") ||
    -1;

  // Closes any previous active options
  if (optionContainerActive.value) {
    if (optionsElem.value.style.display === "block") {
      optionsElem.value.style.display = "none";
      optionContainerActive.value = false;
    }
    if (elemKey && optionsElem.value?.getAttribute("data-key") === elemKey) {
      return;
    }
  }
  if (event?.target?.className === "options-container") {
    optionsElem.value = event.target.lastElementChild;
  } else if (event?.target?.className === "options-button") {
    optionsElem.value = event.target.nextElementSibling;
  }
  if (
    optionsElem.value.style.display === "none" ||
    optionsElem.value.style.display === ""
  ) {
    optionsElem.value.style.display = "block";
    optionContainerActive.value = true;
  } else if (optionsElem.value.style.display === "block") {
    optionsElem.value.style.display = "none";
    optionContainerActive.value = false;
  }
};

const handleListUpdate = (index, event) => {
  event.stopPropagation();
  if (event?.target?.className === "opt-edit") {
    handleOptions(event);
  }
  emit("update-list", index);
};

const handleListDelete = (index, event) => {
  event.stopPropagation();
  if (event?.target?.className === "opt-delete") {
    handleOptions(event);
  }
  emit("delete-list", index);
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
  flex-grow: 1;
}
.options-container {
  z-index: 5;
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
