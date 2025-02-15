<template>
  <div>
    <AddTodo class="add-todo" v-model="todoList" />
  </div>
</template>

<script setup>
import { ref } from "vue";
import AddTodo from "./AddTodo.vue";

const todoList = ref(["create todo", "design todo"]);
</script>

<style scoped>
.add-todo {
  position: fixed;
  bottom: 2%;
  left: 50%;
  transform: translateX(-50%);
}
</style>
