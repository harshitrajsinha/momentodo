<template>
  <div class="main-container">
    <TodoList
      v-model:todo-list-display="todoLists"
      v-model:todo-data="todoData"
      v-model:last-todo-item="todoData[todoData.length - 1]"
      class="left-section"
      @todoItem="showTaskLists"
    />
    <div v-if="!toShowSection" :class="['default-txt']">
      <div>"A goal without a plan is just a wish."</div>
      <div class="author-name">- Antoine de Saint-Exupéry</div>
    </div>
    <TaskList
      v-if="toShowSection"
      ref="taskListComp"
      v-model:task-list-data="taskLists.value"
      :class="['mid-section']"
      :style="{ maxWidth: listMaxWidth }"
      @getListId="showTaskDetail"
    />
    <TaskDetails
      v-if="toShowSection"
      ref="taskDetails"
      v-model:allTaskData="taskLists.value"
      :class="['right-section', { active: toShowTaskDetails }]"
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
let todoLists = ref([]);
let toShowTaskDetails = ref(false);
let listMaxWidth = ref("80%");
let toShowSection = ref(false);

let todoData = ref([
  {
    icon: "🏠",
    title: "Home",
    "task-list": [
      {
        "is-completed": false,
        title: "Add Sugar",
        "task-priority": "medium",
        notes: "testing",
      },
      {
        "is-completed": false,
        title: "Add Spices",
        "task-priority": "medium",
        notes: "testing",
      },
      {
        "is-completed": true,
        title: "Add Salt",
        "task-priority": "medium",
        notes: "testing",
      },
    ],
  },
  {
    icon: "😎",
    title: "Tech",
    "task-list": [
      {
        "is-completed": false,
        title: "Add Veggies",
        "task-priority": "urgent",
        notes:
          "testing testing testing testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testingtesting testing",
      },
    ],
  },
]);

const showTaskLists = (listId) => {
  toShowSection.value = true;
  taskLists.value = computed(() => todoData.value[listId]["task-list"]);
  if (toShowTaskDetails.value === true) handleModalClose(false);
};

todoLists.value = computed(() =>
  todoData.value.map((elem) => {
    return {
      icon: elem["icon"],
      title: elem["title"],
      "task-count": elem["task-list"].filter(
        (elem) => elem["is-completed"] === false
      ).length,
    };
  })
);

const showTaskDetail = (listId) => {
  if (taskDetails.value) {
    taskDetails.value.getCurrentListData(listId);
    toShowTaskDetails.value = true;
    listMaxWidth.value = "50%";
  }
};

const handleModalClose = (data) => {
  if (data === false) {
    toShowTaskDetails.value = false;
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
.default-txt {
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

.default-txt .author-name {
  font-size: 2rem;
  font-style: italic;
}

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
