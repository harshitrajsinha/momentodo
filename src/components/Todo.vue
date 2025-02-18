<template>
  <div class="main-container">
    <TodoList v-model:todo-list-data="todoData" class="left-section" />
    <TaskList
      ref="taskListComp"
      v-model:task-list-data="taskLists.value"
      :test="taskLists"
      class="mid-section"
      :style="{ maxWidth: listMaxWidth }"
      @getListId="showTaskDetail"
    />
    <TaskDetails
      ref="taskDetails"
      v-model:allTaskData="taskLists.value"
      :class="['right-section', { active: toShow }]"
      @close-modal="handleModalClose"
    />
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, onUpdated, computed } from "vue";
import TaskList from "./TaskList.vue";
import TaskDetails from "./TaskDetails.vue";
import TodoList from "./TodoList.vue";
let taskDetails = ref(null);
let taskListComp = ref(null);
let taskLists = ref([]);
let toShow = ref(false);
let listMaxWidth = ref("80%");

let todoData = ref([
  {
    icon: "😁",
    title: "Home",
    "task-list": {
      "is-completed": true,
      title: "Add Sugar",
      "task-priority": "medium",
      notes: "testing",
    },
  },
  {
    icon: "😎",
    title: "Tech",
    "task-list": {
      "is-completed": false,
      title: "Add Veggies",
      "task-priority": "urgent",
      notes:
        "testing testing testing testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testing",
    },
  },
]);

if (taskListComp.value) {
  const id = taskListComp.value.listId;
  console.log("id", id);
}

taskLists.value = computed(() =>
  todoData.value.map((elem) => {
    return elem["task-list"];
  })
);

const showTaskDetail = (listId) => {
  if (taskDetails.value) {
    taskDetails.value.getCurrentListData(listId);
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

// functionality to save data before page reloads or tab closes
{
  const handleBeforeUnload = (event) => {
    // event.preventDefault(); // Some browsers require this
    // event.returnValue = "";
    localStorage.setItem("harshit", "harshit");
    console.log("Page is about to refresh or close!");
  };

  onMounted(() => {
    window.addEventListener("beforeunload", handleBeforeUnload);
  });

  onBeforeUnmount(() => {
    window.removeEventListener("beforeunload", handleBeforeUnload);
  });
}
</script>

<style scoped>
.main-container {
  overflow: hidden;
  display: flex;
  position: relative;
}

.left-section {
  width: 23%;
  height: calc(100vh - 2rem);
}

.mid-section {
  flex-grow: 1;
}

.right-section {
  width: 0%;
  position: relative;
  right: -50vw;
  transition: right 1.5s;
}

.right-section.active {
  position: relative;
  right: 0;
  width: 25%;
  margin-top: 1rem;
  margin-right: 1rem;
  border-radius: 0.5rem;
}
</style>
