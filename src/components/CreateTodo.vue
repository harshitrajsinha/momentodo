<template>
  <div class="create-todo-container">
    <Task
      v-model:tasklist="taskListModel"
      :class="['add-todo', { active: toShow }]"
      ref="taskCompInstance"
    />
    <button class="create-todo-container__create-btn" @click="showAddTodo">
      <font-awesome-icon
        :style="{
          transform: `rotate(${rotation}deg)`,
          transition: `transform 1s`,
          marginRight: `5px`,
        }"
        :icon="['fas', 'plus']"
      />
      Create new task
    </button>
  </div>
</template>

<script setup>
import { ref, defineModel } from "vue";
import Task from "./Task.vue";

const taskCompInstance = ref(null);
let toShow = ref(false);
let rotation = ref(90);
let taskListModel = defineModel();
const showAddTodo = () => {
  if (taskCompInstance.value) {
    taskCompInstance.value.resetTaskFields();
  }
  toShow.value = !toShow.value;
  console.log(toShow.value);
  rotation.value = 90 - rotation.value;
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

.create-todo-container__create-btn {
  font-size: 1rem;
  position: relative;
  margin-top: 0.5rem;
  border-radius: 1.5rem;
  color: white;
  background-color: black;
  cursor: pointer;
  width: -webkit-fill-available;
  border: none;
  padding: 0.8rem;
  z-index: 1;
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
</style>
