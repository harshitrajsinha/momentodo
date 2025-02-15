<template>
  <div class="todo-container">
    <div class="todo-container__elem todo-container__create-task">
      <input class="create-task__is-completed" type="checkbox" />
      <input
        class="create-task__text-input"
        v-model="resetValue"
        ref="focusOnInput"
        @blur="addNewTask"
        placeholder="Create new task"
      />
    </div>
    <select class="todo-container__elem">
      <option value="" disabled selected>Add priority</option>
      <option value="urgent">Urgent</option>
      <option value="medium">Medium</option>
      <option value="low">Low</option>
    </select>
    <textarea
      class="todo-container__elem todo-container__add-notes"
      placeholder="Add notes"
    ></textarea>
    <button class="todo-container__elem todo-container__add-task-btn">
      Create new task
    </button>
  </div>
</template>

<script setup>
import { defineProps, ref, onMounted } from "vue";
const focusOnInput = ref(null);
const resetValue = ref("");
let model = defineModel();
onMounted(() => {
  if (focusOnInput.value) {
    focusOnInput.value.focus();
  }
});

const addNewTask = (event) => {
  if (event.target.value !== "") {
    model.value.push(event.target.value);
    resetValue.value = "";
  }
};
</script>

<style scoped>
.todo-container {
  width: 25vw;
  max-width: 25vw;
  margin: 0 auto;
  padding: 1rem;
  background-color: white;
  border-radius: 0.5rem;
}

.todo-container__elem {
  border-radius: 0.5rem;
  background-color: #efecec;
  width: -webkit-fill-available;
  border: none;
  padding: 0.8rem;
  margin-bottom: 0.5rem;
}

.todo-container__create-task {
  display: flex;
  gap: 0.5rem;
  border: none;
}

.create-task__is-completed {
  transform: scale(1.5);
}

.create-task__text-input {
  width: -webkit-fill-available;
  border: none;
  background-color: transparent;
  outline: none;
}

.todo-container__add-notes {
  resize: none;
  min-height: 8rem;
}

.todo-container__add-task-btn {
  border-radius: 1.5rem;
  color: white;
  background-color: black;
  cursor: pointer;
}
</style>
