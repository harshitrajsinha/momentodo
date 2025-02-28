<template>
  <div ref="isModalOpen" class="task-container">
    <div class="task-container__elem task-container__create-task">
      <input
        v-model="taskCompleted"
        id="task-checkbox"
        class="create-task__is-completed"
        type="checkbox"
        @change="addNewTask"
      />
      <input
        class="create-task__text-input"
        id="task-input"
        type="'text'"
        maxlength="50"
        v-model="taskTitle"
        ref="focusOnInput"
        placeholder="Create new task"
        @input="addNewTask"
      />
      <button class="create-task__emoji-btn" @click="toggleEmojiPicker">
        😃
      </button>
      <EmojiPicker
        v-if="showEmojiPicker"
        :display-recent="true"
        :native="false"
        @select="onSelectEmoji"
      />
    </div>
    <textarea
      v-model="taskNotes"
      :style="{ marginBottom: 0 }"
      class="task-container__elem task-container__add-notes"
      id="task-notes"
      placeholder="Add notes"
      @blur="addNewTask"
    ></textarea>
    <select
      v-model="taskPriority"
      :style="{ borderRadius: '2rem' }"
      class="task-container__elem"
      id="task-priority"
      @change="addNewTask"
    >
      <option value="" disabled selected>Add priority</option>
      <option value="urgent">Urgent</option>
      <option value="medium">Medium</option>
      <option value="low">Low</option>
    </select>
  </div>
</template>

<script setup>
import EmojiPicker from "vue3-emoji-picker";
import "vue3-emoji-picker/css";
import {
  defineProps,
  ref,
  onMounted,
  defineExpose,
  onUpdated,
  onUnmounted,
  nextTick,
} from "vue";
const props = defineProps(["toReset"]);
const resetVal = ref(props.toReset);
const initalTaskCompleted = false;
const initalTaskTitle = "";
const initalTaskPriority = "";
const initalTaskNotes = "";
let isModalOpen = ref(null);
let showEmojiPicker = ref(false);
let recentlyAddedList = ref(false);
let taskList = defineModel("task-list");
let lastListItem = defineModel("last-list-item");

const focusOnInput = ref(null);
let taskCompleted = ref(initalTaskCompleted);
let taskTitle = ref(initalTaskTitle);
let taskPriority = ref(initalTaskPriority);
let taskNotes = ref(initalTaskNotes);
const emit = defineEmits(["toShowModal"]);
let newTaskObj = ref({
  "is-completed": taskCompleted.value,
  title: taskTitle.value,
  "task-priority": taskPriority.value,
  notes: taskNotes.value,
});

const onSelectEmoji = (emoji) => {
  taskTitle.value = taskTitle.value + emoji["i"];
  addNewTask({ target: { id: "task-input" } });
  showEmojiPicker.value = !showEmojiPicker.value;
};

const toggleEmojiPicker = () => {
  showEmojiPicker.value = !showEmojiPicker.value;
};

// Function to reset field values of create task container
const resetTaskFields = () => {
  if (showEmojiPicker.value) {
    showEmojiPicker.value = false;
  }
  taskCompleted.value = initalTaskCompleted;
  taskTitle.value = initalTaskTitle;
  taskPriority.value = initalTaskPriority;
  taskNotes.value = initalTaskNotes;
  newTaskObj.value = {
    "is-completed": taskCompleted.value,
    title: taskTitle.value,
    "task-priority": taskPriority.value,
    notes: taskNotes.value,
  };
};
defineExpose({ resetTaskFields });

onMounted(() => {
  if (focusOnInput.value) {
    focusOnInput.value.focus();
  }
});

onUpdated(async () => {
  await nextTick(); // Ensures DOM updates before checking classList

  if (isModalOpen.value && isModalOpen.value.classList.contains("active")) {
    document.body.addEventListener("click", toCloseTaskModal);
  }
});

onUnmounted(() => {
  document.body.removeEventListener("click", toCloseTaskModal);
});

// Close createTaskModal if clicked outside
const toCloseTaskModal = (event) => {
  if (!isModalOpen.value.classList.contains("active")) {
    resetTaskFields();
    return;
  }
  if (
    !(event?.target?.nodeName === "BUTTON") &&
    !event?.target?.classList?.contains("create-list-btn") &&
    !event?.target?.parentElement?.classList?.contains(
      "task-container__elem"
    ) &&
    !event?.target?.parentElement?.classList?.contains(
      "create-todo-container"
    ) &&
    !event?.target?.parentElement?.classList?.contains("task-container") &&
    !event?.target?.parentElement?.classList?.contains("v3-search") &&
    !event?.target?.parentElement?.parentElement?.classList?.contains(
      "v3-emojis"
    )
  ) {
    recentlyAddedList.value = false;
    emit("toShowModal");
    document.body.removeEventListener("click", toCloseTaskModal);
  }
};

const addNewTask = (event) => {
  if (event.target.id === "task-checkbox") {
    newTaskObj.value["is-completed"] = taskCompleted.value;
  } else if (event.target.id === "task-notes" && taskNotes.value !== "") {
    newTaskObj.value["notes"] = taskNotes.value;
  } else if (event.target.id === "task-priority") {
    newTaskObj.value["task-priority"] = taskPriority.value;
  } else if (event.target.id === "task-input" && taskTitle.value !== "") {
    newTaskObj.value["title"] = taskTitle.value;
  }

  // Updating source data with new item
  if (newTaskObj.value["title"] !== "") {
    if (newTaskObj.value["title"].length === 1) {
      if (!recentlyAddedList.value) {
        recentlyAddedList.value = true;
        taskList.value.push(newTaskObj.value); // creating new item in source
      } else {
        lastListItem.value = newTaskObj.value; // update last item
      }
    } else if (newTaskObj.value["title"].length > 1) {
      // update last item
      lastListItem.value = newTaskObj.value;
    }
  }
};
</script>

<style scoped>
input,
select,
textarea {
  font-family: monospace;
}
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

.create-task__emoji-btn {
  border: none;
  transform: scale(1.5);
  cursor: pointer;
}

.v3-emoji-picker {
  position: absolute;
  right: -17rem;
  top: 1rem;
}
</style>
