<template>
  <div class="main-container">
    <TodoList v-model:todo-list-data="todoData" class="left-section" />
    <div class="mid-section" :style="{ maxWidth: listMaxWidth }">
      <DisplayList
        ref="displayList"
        v-model:list-model="taskLists.value"
        listStyles="task-list-style"
        listContainerStyle="list-container-style"
        @getListId="getListId"
      >
        <template #list-checkbox="{ completionStatus, indexVal }">
          <input
            class="task-lists__is-completed"
            type="checkbox"
            :checked="completionStatus"
            @click="toggleCheckbox(indexVal)"
          />
        </template>
      </DisplayList>

      <CreateTodo v-model="taskLists.value" />
    </div>
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
import CreateTodo from "./CreateTodo.vue";
import DisplayList from "./DisplayList.vue";
import TaskList from "./TaskList.vue";
import TaskDetails from "./TaskDetails.vue";
import TodoList from "./TodoList.vue";
let taskDetails = ref(null);
let displayList = ref(null);
let currentList = ref({});
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

taskLists.value = computed(() =>
  todoData.value.map((elem) => {
    return elem["task-list"];
  })
);

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

const getListId = (data) => {
  if (taskDetails.value) {
    taskDetails.value.getCurrentListData(data);
    toShow.value = true;
    listMaxWidth.value = "50%";
  }
};

const toggleCheckbox = (id) => {
  const isCompleted = todoData.value[id]["task-list"]["is-completed"];
  todoData.value[id]["task-list"]["is-completed"] = !isCompleted;
};

const handleModalClose = (data) => {
  if (data === false) {
    toShow.value = false;
    listMaxWidth.value = "80%";
  }
};
</script>

<style scoped>
::v-deep(div.list-container-style) {
  margin: 0 auto;
  padding: 2rem 6rem;
}
::v-deep(li.task-list-style) {
  padding: 0.8rem;
  margin-bottom: 0.5rem;
  border-radius: 0.5rem;
  gap: 1rem;
  font-size: 1rem;
  font-weight: 600;
}
.task-lists__is-completed {
  transform: scale(1.5);
}

::v-deep(li.task-list-style input[type="checkbox"]:checked) ~ * {
  text-decoration: line-through;
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
