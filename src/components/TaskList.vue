<template>
  <div>
    <DisplayItemList
      ref="displayList"
      v-model:list-model="taskLists"
      listStyles="task-list-style"
      listContainerStyle="list-container-style"
      @getListId="getClickedListId"
      @getTitleKey="updateTitle"
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

const emit = defineEmits(["getListId", "reloadTaskDetail"]);
let taskLists = defineModel("task-list-data");

const getClickedListId = (id, isCheckbox) => {
  emit("getListId", id, isCheckbox);
};

const updateTitle = (key) => {
  if (taskLists.value) {
    const list = document
      .querySelector(".mid-section")
      .querySelector(`[data-key="${key}"]`);
    list.contentEditable = "true";
    list.addEventListener("keyup", function () {
      if (list.textContent.length >= 50) {
        list.textContent = list.textContent.slice(0, 50);
      }
    });
    list.addEventListener("focusout", function () {
      list.contentEditable = "false";
      taskLists.value[key]["title"] = list.textContent;
      emit("reloadTaskDetail", key);
    });
  }
};

const toggleCheckbox = (id) => {
  const isCompleted = taskLists.value[id]["is-completed"];
  taskLists.value[id]["is-completed"] = !isCompleted;
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

::v-deep(li.task-list-style input[type="checkbox"]:checked) ~ * {
  text-decoration: line-through;
}
</style>
