<template>
  <div class="container">
    <ul>
      <li v-for="(taskList, index) in taskLists" :key="index" class="task-list">
        <input class="task-list__is-completed" type="checkbox" /><span>{{
          taskList.title
        }}</span>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from "vue";
let taskLists = defineModel();
</script>

<style scroped>
.container {
  width: 80vh;
  max-width: 80vh;
  margin: 0 auto;
  padding: 2rem;
}
.task-list {
  background-color: white;
  padding: 0.8rem;
  margin-bottom: 0.5rem;
  border-radius: 0.5rem;
  display: flex;
  gap: 1rem;
  font-size: 1.2rem;
}

.task-list__is-completed {
  transform: scale(1.5);
}
</style>
