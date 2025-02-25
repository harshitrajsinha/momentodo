<template>
  <div>
    <DisplayItemList
      ref="displayList"
      v-model:list-model="taskLists"
      listStyles="task-list-style"
      listContainerStyle="list-container-style"
      @getListId="getClickedListId"
      @getTitleKey="updateTitle"
      @deleteList="deleteList"
    >
      <!-- slot component -->
      <template #list-checkbox="{ completionStatus, indexVal }">
        <input
          class="task-lists__is-completed"
          type="checkbox"
          :checked="completionStatus"
          @click="toggleCheckbox(indexVal)"
        />
      </template>
    </DisplayItemList>
    <CreateTaskList v-model:task-list-model="taskLists" />
  </div>
</template>

<script setup>
import DisplayItemList from "./DisplayItemList.vue";
import CreateTaskList from "./CreateTaskList.vue";
import { onUpdated, ref } from "vue";

let contentEditable = ref(false);
let isCheckbox = ref(false);
let isOptions = ref(false);

const emit = defineEmits([
  "getListId",
  "reloadTaskDetail",
  "close-taskD-modal",
]);
let taskLists = defineModel("task-list-data");

const getClickedListId = (id, event) => {
  if (event?.target?.type === "checkbox") {
    isCheckbox.value = true;
    emit("getListId", id, isCheckbox.value);
    return;
  }
  if (event?.target?.className === "options-container") {
    return;
  }
  if (event?.target?.className === "options-button") {
    return;
  } else if (event?.target?.className === "opt-edit") {
    return;
  } else if (event?.target?.className === "opt-delete") {
    return;
  } else if (
    event?.target?.className === "list-name" &&
    contentEditable.value
  ) {
    return;
  }
  isCheckbox.value = false;
  emit("getListId", id, isCheckbox.value);
};

const updateTitle = (key) => {
  if (taskLists.value) {
    const list = document
      .querySelector(".mid-section")
      .querySelector(`[data-key="${key}"]`);
    list.contentEditable = "true";
    contentEditable.value = true;
    list.focus();
    list.addEventListener("keyup", function () {
      if (list.textContent.length >= 50) {
        contentEditable.value = false;
        list.textContent = list.textContent.slice(0, 49);
      }
    });
    list.addEventListener("focusout", function () {
      list.contentEditable = "false";
      contentEditable.value = true;
      taskLists.value[key]["title"] = list.textContent;
      emit("reloadTaskDetail", key);
    });
  }
};

const toggleCheckbox = (id) => {
  const isCompleted = taskLists.value[id]["is-completed"];
  taskLists.value[id]["is-completed"] = !isCompleted;
};

const deleteList = (id) => {
  taskLists.value.splice(id, 1);
  emit("close-taskD-modal", false);
};
</script>

<style scoped>
::v-deep(div.list-container-style) {
  margin: 0 auto;
  padding: 2rem 6rem;
}
::v-deep(li.task-list-style) {
  padding: 0.8rem;
  margin-bottom: 0.5rem;
  border-radius: 0.5rem;
  gap: 1rem;
  font-size: 1rem;
  font-weight: 600;
}

.task-lists__is-completed {
  transform: scale(1.5);
}

::v-deep(li.task-list-style input[type="checkbox"]:checked) ~ .list-name {
  text-decoration: line-through;
}
</style>
