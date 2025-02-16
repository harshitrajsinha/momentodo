<template>
  <div class="container">
    <ul>
      <li
        v-for="(taskList, index) in taskLists"
        :key="index"
        class="task-lists"
        @click="sendIndex(index)"
      >
        <input class="task-lists__is-completed" type="checkbox" />
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
  margin: 0 auto;
  padding: 2rem 6rem;
}

li span {
  width: 100%;
  overflow-x: scroll;
}

li span::-webkit-scrollbar {
  height: auto;
  background-color: white;
}

.task-lists {
  background-color: white;
  padding: 0.8rem;
  padding-bottom: 0;
  margin-bottom: 0.5rem;
  border-radius: 0.5rem;
  display: flex;
  gap: 1rem;
  align-items: baseline;
  font-size: 1.2rem;
  cursor: pointer;
  font-weight: 600;
}

.task-lists__is-completed {
  transform: scale(1.5);
}
</style>
