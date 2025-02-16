<template>
  <div class="main-container">
    <Groups class="left-section" />
    <div class="mid-section" :style="{ maxWidth: listMaxWidth }">
      <TaskList v-model="taskLists" @get-index="getListId" />
      <CreateTodo v-model="taskLists" />
    </div>
    <TaskDetails
      ref="taskDetails"
      v-model:allTaskData="taskLists"
      :class="['right-section', { active: toShow }]"
      @close-modal="handleModalClose"
    />
  </div>
</template>

<script setup>
import { ref } from "vue";
import CreateTodo from "./CreateTodo.vue";
import TaskList from "./TaskList.vue";
import TaskDetails from "./TaskDetails.vue";
import Groups from "./Groups.vue";
let taskDetails = ref(null);
let currentList = ref({});
let toShow = ref(false);
let listMaxWidth = ref("80%");
const taskLists = ref([
  {
    "is-completed": true,
    title: "Add Sugar",
    "task-priority": "medium",
    notes: "testing",
  },
  {
    "is-completed": false,
    title: "Add Veggies",
    "task-priority": "urgent",
    notes:
      "testing testing testing testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testing",
  },
  {
    "is-completed": true,
    title: "Add spices",
    "task-priority": "low",
    notes: "testing",
  },
]);

const getListId = (data) => {
  currentList = { ...taskLists.value[data] };
  if (taskDetails.value) {
    taskDetails.value.getCurrentListData(data);
    toShow.value = true;
    listMaxWidth.value = "50%";
  }
};

const handleModalClose = (data) => {
  if (data === false) {
    toShow.value = false;
    listMaxWidth.value = "80%";
  }
};
</script>

<style>
.main-container {
  overflow: hidden;
  display: flex;
  position: relative;
}

.left-section {
  width: 20%;
}

.mid-section {
  flex-grow: 1;
}

.right-section {
  width: 0%;
  position: relative;
  right: -100vw;
  transition: right 1.5s;
}

.right-section.active {
  position: relative;
  right: 0;
  width: 20%;
  margin-top: 1rem;
  margin-right: 1rem;
  border-radius: 0.5rem;
}
</style>
