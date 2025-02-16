<template>
  <div class="container">
    <ul>
      <li
        v-for="(taskList, index) in taskLists"
        :key="index"
        class="task-lists"
        @click="sendIndex(index)"
        :style="{
          border: taskList['is-completed']
            ? '1px solid green'
            : taskList['task-priority'] === ''
            ? '1px solid blue'
            : taskList['task-priority'] === 'urgent'
            ? '1px solid #fe3c3c'
            : taskList['task-priority'] === 'medium'
            ? '1px solid orange'
            : '1px solid #f3f33a',
          textDecoration: taskList['is-completed'] ? 'line-through' : '',
        }"
      >
        <input
          ref="completionInput"
          class="task-lists__is-completed"
          type="checkbox"
          :checked="taskList['is-completed']"
          @click="toggleCheckbox(index)"
        />
        <span>{{ taskList.title }}</span>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { defineEmits, ref } from "vue";
let completionInput = ref(null);
let taskLists = defineModel("tasklists");

const toggleCheckbox = (id) => {
  const isCompleted = taskLists.value[id]["is-completed"];
  if (completionInput.value) {
    taskLists.value[id]["is-completed"] = !isCompleted;
  }
};
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
  font-size: 1rem;
  cursor: pointer;
  font-weight: 600;
}

.task-lists__is-completed {
  transform: scale(1.5);
}
</style>
