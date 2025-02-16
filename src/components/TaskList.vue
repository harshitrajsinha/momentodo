<template>
  <div class="container">
    <ul>
      <li
        v-for="(taskList, index) in taskLists"
        :key="index"
        class="task-list"
        @click="sendIndex(index)"
      >
        <input class="task-list__is-completed" type="checkbox" />
        <span>{{ taskList.title }}</span>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { defineEmits } from "vue";
let taskLists = defineModel();
const emit = defineEmits(["get-index"]);

const sendIndex = (index) => {
  emit("get-index", index);
};
</script>

<style scroped>
.container {
  /* width: 90%;
  max-width: 80%; */
  margin: 0 auto;
  padding: 2rem;
}
.task-list__title {
  border: 2px solid blue;
}
.task-list {
  background-color: white;
  padding: 0.8rem;
  margin-bottom: 0.5rem;
  border-radius: 0.5rem;
  display: flex;
  gap: 1rem;
  font-size: 1.2rem;
  cursor: pointer;
}

.task-list__is-completed {
  transform: scale(1.5);
}
</style>
