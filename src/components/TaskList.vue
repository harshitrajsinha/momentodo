<template>
  <div>
    <div v-if="!taskLists.length" :class="['task-section__default-txt']">
      <div>"The secret of getting ahead is getting started."</div>
      <div class="task-section__author-name">- Mark Twain</div>
    </div>
    <DisplayListItems
      ref="taskListItems"
      v-model:list-model="taskLists"
      listStyles="task-list-style"
      listContainerStyle="list-container-style"
      @get-list-id="getClickedListId"
      @update-list="updateTaskItemTitle"
      @delete-list="deleteTaskItem"
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
    </DisplayListItems>
    <CreateTaskList
      ref="createTaskListComp"
      v-model:task-list-model="taskLists"
    />
  </div>
</template>

<script setup>
import DisplayListItems from "./DisplayListItems.vue";
import CreateTaskList from "./CreateTaskList.vue";
import { ref, onUnmounted, defineExpose } from "vue";

let createTaskListComp = ref(null);
let activeEditableTask = ref(null);
let contentEditable = ref(false);
let isCheckbox = ref(false);
let taskListItems = ref(null);
const emit = defineEmits([
  "get-list-id",
  "reload-task-details",
  "close-task-details",
]);
let taskLists = defineModel("task-list-data");

defineExpose({ taskListItems, createTaskListComp });

const getClickedListId = (id, event) => {
  if (event?.target?.type === "checkbox") {
    isCheckbox.value = true;
    emit("get-list-id", id, isCheckbox.value);
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
  taskListItems.value.lists.forEach((list) => {
    list.style.border = "none"; // reset all borders
  });
  taskListItems.value.lists[id].style.border = "1px solid black";
  isCheckbox.value = false;
  emit("get-list-id", id, isCheckbox.value);
};

const restrictTaskTitleLength = (list) => {
  if (list.textContent.length >= 50) {
    contentEditable.value = false;
    list.textContent = list.textContent.slice(0, 49);
  }
};

const saveTaskTitle = (list, key) => {
  list.contentEditable = "false";
  contentEditable.value = false;
  taskLists.value[key]["title"] = list.textContent;
  emit("reload-task-details", key);
};

const updateTaskItemTitle = (key) => {
  if (taskListItems.value) {
    activeEditableTask.value = taskListItems.value.listTitle[key];
    activeEditableTask.value.contentEditable = "true";
    contentEditable.value = true;
    activeEditableTask.value.focus();
    activeEditableTask.value.addEventListener("keyup", () =>
      restrictTaskTitleLength(activeEditableTask.value)
    );
    activeEditableTask.value.addEventListener("focusout", () =>
      saveTaskTitle(activeEditableTask.value, key)
    );
  }
};

const toggleCheckbox = (id) => {
  const isCompleted = taskLists.value[id]["is-completed"];
  taskLists.value[id]["is-completed"] = !isCompleted;
};

const deleteTaskItem = (id) => {
  taskLists.value.splice(id, 1);
  emit("close-task-details", true);
};

onUnmounted(() => {
  if (activeEditableTask.value) {
    activeEditableTask.value.removeEventListener(
      "keyup",
      restrictTaskTitleLength
    );
    activeEditableTask.value.removeEventListener("focusout", saveTaskTitle);
  }
});
</script>

<style scoped>
.task-section__default-txt {
  color: #888585a7;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  position: relative;
  text-align: center;
  font-size: 2.5rem;
  width: auto;
  top: 50%;
  transform: translateY(-60%);
}

.task-section__author-name {
  font-size: 1.5rem;
  font-style: italic;
}

::v-deep(div.list-container-style) {
  max-height: 78vh;
  overflow-y: auto;
  margin: 5rem auto 0;
  padding: 2rem 6rem;
}

::v-deep(div.list-container-style)::-webkit-scrollbar {
  height: 0.5rem;
  width: 0.6rem;
}

::v-deep(div.list-container-style)::-webkit-scrollbar-thumb {
  border-radius: 0.5rem;
  background-color: rgba(167, 163, 163, 0.707);
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

@media screen and (min-width: 1024px) and (max-width: 1300px) {
  ::v-deep(div.list-container-style) {
    padding: 2rem 4rem;
  }
}

@media screen and (min-width: 541px) and (max-width: 1024px) {
  ::v-deep(div.list-container-style) {
    padding: 2rem 2rem;
  }
}

@media screen and (max-width: 540px) {
  .task-section__default-txt {
    font-size: 1.5rem;
  }

  .task-section__author-name {
    font-size: 1rem;
  }

  ::v-deep(div.list-container-style) {
    margin: 0;
    padding: 1rem 1rem;
  }
}
</style>
