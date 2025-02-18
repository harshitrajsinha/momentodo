<template>
  <div class="create-todo-container">
    <Task
      v-model:tasklist="taskListModel"
      :class="['add-todo', { active: toShow }]"
      ref="taskCreateInstance"
    />
    <CreateListBtn
      btnText="Create new tasks"
      class="create-task-btn-style"
      @toShowModal="showCreateTaskModal"
    />
  </div>
</template>

<script setup>
import { ref, defineModel } from "vue";
import Task from "./Task.vue";
import CreateListBtn from "./CreateListBtn.vue";

const taskCompInstance = ref(null);
let toShow = ref(false);
let taskListModel = defineModel();
const showCreateTaskModal = () => {
  // if (taskCompInstance.value) {
  //   taskCompInstance.value.resetTaskFields();
  // }
  toShow.value = !toShow.value;
};
</script>

<style scoped>
.create-todo-container {
  position: fixed;
  bottom: 2%;
  left: 50%;
  transform: translateX(-50%);
  width: 25vw;
  max-width: 25vw;
  margin: 0 auto;
}

.add-todo {
  position: relative;
  bottom: -100vh;
  transition: bottom 1.5s;
}

.add-todo.active {
  position: relative;
  bottom: 0vh;
}

.create-task-btn-style {
  position: relative;
  width: -webkit-fill-available;
}
</style>
