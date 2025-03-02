<!-- 
=== Template structure ===
    TodoList
    Default quote, if mid-right is inactive
    TaskList
    TaskDetails
-->
<template>
  <div class="main-container">
    <TodoList
      :class="['left-section', { hide: hideLSForMobile }]"
      v-model:todo-data="todoData"
      @todo-list-id="showTaskLists"
      @delete-todo-item="deleteTodoItem"
    />
    <div v-if="!toShowMidnRightSection" :class="['default-quote']">
      <div>"A goal without a plan is just a wish."</div>
      <div class="default-quote__author-name">- Antoine de Saint-Exupéry</div>
    </div>
    <TaskList
      ref="taskListComponent"
      v-if="toShowMidnRightSection"
      v-model:task-list-data="taskLists.value"
      :class="['mid-section', { hide: hideMSForMobile }]"
      :style="{ maxWidth: listMaxWidth }"
      @get-list-id="showTaskDetail"
      @reload-task-details="showTaskDetail"
      @close-task-details="hideTaskDetails"
    />
    <TaskDetails
      v-if="toShowMidnRightSection"
      ref="taskDetails"
      v-model:all-task-data="taskLists.value"
      :class="[
        'right-section',
        { active: toShowTaskDetails, inactive: toCloseTaskDetails },
        { hide: hideRSForMobile },
      ]"
      @close-task-details="hideTaskDetails"
    />
  </div>
</template>

<script setup>
import { ref, onMounted, computed, watch } from "vue";
import TaskList from "./TaskList.vue";
import TaskDetails from "./TaskDetails.vue";
import TodoList from "./TodoList.vue";

let bodyWidth = ref(0);
let hideLSForMobile = ref(false); // hide left section
let hideMSForMobile = ref(false); // hide mid section
let hideRSForMobile = ref(false); // hide right section
let todoData = ref([]); // Array of all todo items and their respective tasks
let taskListComponent = ref(null);
let taskDetails = ref(null);
let taskLists = ref([]);
let toShowTaskDetails = ref(false);
let toCloseTaskDetails = ref(false);
let listMaxWidth = ref("80%");
let toShowMidnRightSection = ref(false);

// Set todo data on mounting
onMounted(() => {
  bodyWidth.value = document.body.offsetWidth;
  const getTodoData = localStorage.getItem("todo-data");
  if (getTodoData) {
    todoData.value = JSON.parse(getTodoData);
  } else {
    // Sample data
    todoData.value = [
      {
        icon: "🏠",
        title: "Home",
        "task-list": [
          {
            "is-completed": false,
            title: "Add Sugar",
            "task-priority": "medium",
            notes: "Add 1/2 spoon sugar",
          },
          {
            "is-completed": false,
            title: "Add Spices",
            "task-priority": "medium",
            notes: "Add spices according to taste",
          },
          {
            "is-completed": true,
            title: "Add Salt",
            "task-priority": "medium",
            notes: "Add salt according to taste",
          },
        ],
      },
      {
        icon: "😎",
        title: "Tech",
        "task-list": [
          {
            "is-completed": false,
            title: "MomenTodo",
            "task-priority": "urgent",
            notes: "Use this task making application",
          },
        ],
      },
    ];
  }
});

// update localStorage on change in todo data
watch(
  todoData,
  () => {
    localStorage.setItem("todo-data", JSON.stringify(todoData.value));
  },
  { deep: true }
);

const deleteTodoItem = (listId) => {
  toShowMidnRightSection.value = false;
  todoData.value.splice(listId, 1);
};

const showTaskLists = (listId) => {
  if (taskListComponent.value) {
    taskListComponent.value.taskListItems.lists.forEach((list) => {
      list.style.border = "none"; // reset all task list borders
    });
  }
  toShowMidnRightSection.value = true;
  taskLists.value = computed(() => todoData.value[listId]["task-list"]);
  if (bodyWidth.value <= 540) {
    hideLSForMobile.value = true;
    hideMSForMobile.value = false;
    hideRSForMobile.value = true;
  }
  // close any active task details
  if (toShowTaskDetails.value === true) hideTaskDetails(true);
};

// update task list checkbox value
const updateTaskListCheckbox = () => {
  if (taskLists.value.value) {
    taskLists.value.value["is-completed"] =
      !taskLists.value.value["is-completed"];
  }
};

// Executes when any task list is clicked
const showTaskDetail = (taskItemtId, isCheckbox) => {
  if (isCheckbox) {
    updateTaskListCheckbox();
    taskDetails.value.getActiveTaskItemData(taskItemtId);
    return;
  }
  if (taskDetails.value) {
    if (bodyWidth.value <= 540) {
      hideLSForMobile.value = true;
      hideMSForMobile.value = true;
      hideRSForMobile.value = false;
    }
    taskDetails.value.getActiveTaskItemData(taskItemtId);
    toCloseTaskDetails.value = false;
    toShowTaskDetails.value = true;
    listMaxWidth.value = "50%";
    if (taskListComponent.value && bodyWidth.value > 540) {
      taskListComponent.value.createTaskListComp.createTaskContainer.style.transform =
        "translateX(-50%)";
    }
  }
};

// Executes when close button of task detail is clicked
const hideTaskDetails = (toHide) => {
  if (!toShowTaskDetails.value) {
    return;
  }
  if (toHide) {
    if (bodyWidth.value <= 540) {
      hideLSForMobile.value = true;
      hideMSForMobile.value = false;
      hideRSForMobile.value = true;
    }
    toShowTaskDetails.value = false;
    toCloseTaskDetails.value = true;
    listMaxWidth.value = "80%";
    if (taskListComponent.value && bodyWidth.value > 540) {
      taskListComponent.value.createTaskListComp.createTaskContainer.style.transform =
        "translateX(0%)";
    }
  }
};

// functionality to save data before page reloads or tab closes
// {
// const handleBeforeUnload = (event) => {
//   event.preventDefault(); // Some browsers require this
//   console.log("Page is about to refresh or close!");
// };

// onMounted(() => {
//   window.addEventListener("beforeunload", handleBeforeUnload);
// });

// onBeforeUnmount(() => {
//   window.removeEventListener("beforeunload", handleBeforeUnload);
// });
// }
</script>

<style scoped>
.main-container {
  overflow: hidden;
  display: flex;
  position: relative;
}

.default-quote {
  color: #888585a7;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  position: relative;
  text-align: center;
  font-size: 3rem;
  width: calc(100% - 23%);
}

.default-quote__author-name {
  font-size: 1.5rem;
  font-style: italic;
}

.left-section {
  min-width: 21rem;
  max-width: 21rem;
  width: 23%;
  height: calc(100vh - 2rem);
}

@keyframes showTaskLists {
  0% {
    top: -100vh;
    opacity: 0;
  }
  100% {
    top: 0;
    opacity: 1;
  }
}

.mid-section {
  margin: 1rem 0.5rem;
  border-radius: 0.5rem;
  position: relative;
  flex-grow: 1;
  animation: showTaskLists 1.5s ease;
}

@keyframes closeTaskDetails {
  0% {
    right: 0;
    opacity: 1;
    width: 25%;
  }
  100% {
    right: -50vw;
    opacity: 0;
    width: 0%;
  }
}

@keyframes openTaskDetails {
  0% {
    right: -50vw;
    opacity: 0;
  }
  100% {
    right: 0;
    opacity: 1;
  }
}

.right-section {
  width: 0%;
  right: -50vw;
  position: relative;
  height: calc(100vh - 2rem);
}

.right-section.inactive {
  width: 0%;
  right: -50vw;
  position: relative;
  animation: closeTaskDetails 0.5s ease;
}

.right-section.active {
  position: relative;
  right: 0;
  width: 25%;
  margin-top: 1rem;
  margin-right: 1rem;
  border-radius: 0.5rem;
  animation: openTaskDetails 3s ease;
}

@media screen and (min-width: 1024px) and (max-width: 1300px) {
  .right-section {
    padding-left: 1rem;
    padding-right: 1rem;
  }
}

@media screen and (min-width: 541px) and (max-width: 1024px) {
  .left-section {
    min-width: 18rem;
  }
}

@media screen and (max-width: 540px) {
  .left-section {
    min-width: 100vw;
    max-width: 100vw;
    min-height: 100vh;
    max-height: 100vh;
  }
  .mid-section {
    min-width: 100vw;
    max-width: 100vw;
    min-height: 100vh;
    max-height: 100vh;
    margin: 0;
  }
  .right-section {
    right: 0;
    width: 100vw;
    height: 100vh;
    min-width: 100vw;
    min-height: 100vh;
  }

  @keyframes slide {
    0% {
      transform: translateX(0vw);
      display: block;
      z-index: 2;
    }
    100% {
      transform: translateX(-150vw);
      z-index: -1;
      display: none;
    }
  }

  .left-section.hide {
    animation: slide 1.5s ease-out;
    transform: translateX(-150vw);
    display: none;
  }
  .mid-section.hide {
    animation: slide 1.5s ease-out;
    transform: translateX(-150vw);
    display: none;
  }
  .right-section.hide {
    transform: translateX(-150vw);
    display: none;
  }
}
</style>
