<template>
  <div>
    <CreateTodo />
  </div>
</template>

<script setup>
import { ref } from "vue";
import CreateTodo from "./CreateTodo.vue";

const todoList = ref(["create todo", "design todo"]);
</script>
