<template>
  <div class="task-container">
    <div class="task-container__elem task-container__create-task">
      <input class="create-task__is-completed" type="checkbox" />
      <input
        class="create-task__text-input"
        v-model="resetValue"
        ref="focusOnInput"
        @blur="addNewTask"
        placeholder="Create new task"
      />
    </div>
    <select class="task-container__elem">
      <option value="" disabled selected>Add priority</option>
      <option value="urgent">Urgent</option>
      <option value="medium">Medium</option>
      <option value="low">Low</option>
    </select>
    <textarea
      class="task-container__elem task-container__add-notes"
      placeholder="Add notes"
    ></textarea>
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
.task-container {
  padding: 1rem;
  background-color: white;
  border-radius: 0.5rem;
}

.task-container__elem {
  border-radius: 0.5rem;
  background-color: #efecec;
  width: -webkit-fill-available;
  border: none;
  padding: 0.8rem;
  margin-bottom: 0.5rem;
}

.task-container__create-task {
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

.task-container__add-notes {
  resize: none;
  min-height: 8rem;
}
</style>
