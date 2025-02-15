<template>
  <div>
    <TaskList v-model="taskLists" />
    <CreateTodo v-model="taskLists" />
  </div>
</template>

<script setup>
import { ref } from "vue";
import CreateTodo from "./CreateTodo.vue";
import TaskList from "./TaskList.vue";

const taskLists = ref([
  {
    "is-completed": true,
    title: "Add Sugar",
    "task-priority": "medium",
    notes: "testing",
  },
]);
</script>
