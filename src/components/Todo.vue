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
      @reloadTaskDetail="showTaskDetail"
      @close-taskD-modal="hideTaskDetails"
    />
    <TaskDetails
      v-if="toShowSection"
      ref="taskDetails"
      v-model:allTaskData="taskLists.value"
      :class="[
        'right-section',
        { active: toShowTaskDetails, inactive: toCloseTaskDetails },
      ]"
      @close-modal="hideTaskDetails"
    />
  </div>
</template>

<script setup>
import {
  ref,
  onMounted,
  onBeforeUnmount,
  computed,
  watch,
  onUpdated,
} from "vue";
import TaskList from "./TaskList.vue";
import TaskDetails from "./TaskDetails.vue";
import TodoList from "./TodoList.vue";
let taskDetails = ref(null);
let taskListComp = ref(null);
let taskLists = ref([]);
let todoLists = ref([]);
let toShowTaskDetails = ref(false);
let toCloseTaskDetails = ref(false);
let listMaxWidth = ref("80%");
let toShowSection = ref(false);
let todoData = ref([]);

onMounted(() => {
  const getTodoData = localStorage.getItem("todo-data");
  if (getTodoData) {
    todoData.value = JSON.parse(getTodoData);
  } else {
    todoData.value = [
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
            notes: "testing",
          },
        ],
      },
    ];
  }
});

watch(
  todoData,
  () => {
    localStorage.setItem("todo-data", JSON.stringify(todoData.value));
  },
  { deep: true }
);

const showTaskLists = (listId) => {
  toShowSection.value = true;
  taskLists.value = computed(() => todoData.value[listId]["task-list"]);
  if (toShowTaskDetails.value === true) hideTaskDetails(false);
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

const showTaskDetail = (listId, isCheckbox) => {
  if (isCheckbox) {
    if (taskLists.value.value) {
      taskLists.value.value["is-completed"] =
        !taskLists.value.value["is-completed"];
    }
    if (toShowSection.value) {
      taskDetails.value.getCurrentListData(listId);
    }
    return;
  }
  if (taskDetails.value) {
    taskDetails.value.getCurrentListData(listId);
    toCloseTaskDetails.value = false;
    toShowTaskDetails.value = true;
    listMaxWidth.value = "50%";
  }
};

const hideTaskDetails = (data) => {
  if (!toShowTaskDetails.value) {
    return;
  }
  if (data === false) {
    toShowTaskDetails.value = false;
    toCloseTaskDetails.value = true;
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
  font-size: 1.5rem;
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
</style>
