<template>
  <div ref="createTaskContainer" class="create-task-container">
    <Task
      v-model:task-list="taskListModel"
      v-model:last-list-item="taskListModel[taskListModel.length - 1]"
      :class="['task-todo', { active: toShow }]"
      ref="taskCreateInstance"
      @toShowModal="showCreateTaskModal"
    />
    <CreateListBtn
      ref="createListBtn"
      btnText="Create new tasks"
      class="create-task-btn-style"
      @toShowModal="showCreateTaskModal"
    />
  </div>
</template>

<script setup>
import { ref, defineModel, defineExpose } from "vue";
import Task from "./Task.vue";
import CreateListBtn from "./CreateListBtn.vue";

let createTaskContainer = ref(null);
const taskCreateInstance = ref(null);
let toShow = ref(false);
let createListBtn = ref(null);
let taskListModel = defineModel("task-list-model");
defineExpose({ createTaskContainer });

const showCreateTaskModal = () => {
  if (taskCreateInstance.value) {
    taskCreateInstance.value.resetTaskFields();
  }
  createListBtn.value.rotateIcon();
  toShow.value = !toShow.value;
};
</script>

<style scoped>
.create-task-container {
  position: fixed;
  bottom: 2%;
  left: 50%;
  width: 25vw;
  max-width: 25vw;
  margin: 0 auto;
}

.task-todo {
  z-index: -1;
  position: relative;
  bottom: -100vh;
  transition: bottom 1.5s;
}

.add-task.active {
  z-index: 2;
  position: relative;
  bottom: 0vh;
}

.create-task-btn-style {
  position: relative;
  width: -webkit-fill-available;
  z-index: 5;
}
</style>
