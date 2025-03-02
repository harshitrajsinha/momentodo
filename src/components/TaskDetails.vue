<template>
  <div class="task-details-container">
    <div class="task-tags task-container__elem">
      <div
        class="task-tags__priority"
        :style="{
          backgroundColor:
            currentPriority === 'urgent'
              ? '#fe3c3c'
              : currentPriority === 'medium'
              ? 'orange'
              : '#f3f33a',
        }"
      >
        <span class="circle-decor">&#9679;</span>
        {{
          currentPriority === "urgent"
            ? "Urgent"
            : currentPriority === "medium"
            ? "Medium"
            : "Low"
        }}
      </div>
      <div
        class="task-tags__completion"
        :style="{
          backgroundColor: currentCompletionStatus ? '#6fe19a' : '#9e9e9e',
        }"
      >
        <span class="circle-decor">&#9679;</span>
        {{ currentCompletionStatus ? "Completed" : "Not completed" }}
      </div>
    </div>
    <font-awesome-icon
      class="task-details__right-caret"
      :icon="['fas', 'square-caret-right']"
      @click="closeTaskDetailSection"
    />
    <label
      >Title
      <div class="task-container__elem title-container">
        <input
          class="task-details__title"
          type="'text'"
          maxlength="50"
          placeholder="Edit title"
          v-model="currentTitle"
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
    </label>
    <label
      >Priority
      <select
        class="task-details__priority task-container__elem"
        v-model="currentPriority"
      >
        <option value="" disabled selected>Add priority</option>
        <option value="urgent">Urgent</option>
        <option value="medium">Medium</option>
        <option value="low">Low</option>
      </select>
    </label>
    <label
      >Notes
      <textarea
        class="task-container__elem task-details__notes"
        placeholder="Edit notes"
        v-model="currentNotes"
      ></textarea>
    </label>
    <div class="task-details__set-btn">
      <button class="save-btn" @click="saveTaskDetailsData">Save</button>
      <button class="reset-btn" @click="resetTaskDetailsData">Reset</button>
    </div>
  </div>
</template>

<script setup>
import EmojiPicker from "vue3-emoji-picker";
import "vue3-emoji-picker/css";
import { ref, defineEmits } from "vue";

let showEmojiPicker = ref(false);
let activeTaskItemId = ref(-1); // stores active task list id
let currentTitle = ref(""); // stores updated task title
let currentPriority = ref(""); // stores updated task priority
let currentNotes = ref(""); // stores updated task notes
let currentCompletionStatus = ref(false); // stores updated task status
let initalTaskData = ref({}); // stores initial task data
let allData = defineModel("all-task-data");
const emit = defineEmits(["close-task-details"]);

const getActiveTaskItemData = (taskItemtId) => {
  activeTaskItemId.value = taskItemtId;
  initalTaskData.value = allData.value[taskItemtId];
  // initialize with intial values
  currentTitle.value = initalTaskData.value["title"];
  currentPriority.value = initalTaskData.value["task-priority"];
  currentNotes.value = initalTaskData.value["notes"];
  currentCompletionStatus.value = initalTaskData.value["is-completed"];
};

const saveTaskDetailsData = () => {
  allData.value[activeTaskItemId.value]["title"] = currentTitle.value;
  allData.value[activeTaskItemId.value]["task-priority"] =
    currentPriority.value;
  allData.value[activeTaskItemId.value]["notes"] = currentNotes.value;
};

const resetTaskDetailsData = () => {
  currentTitle.value = initalTaskData.value["title"];
  currentPriority.value = initalTaskData.value["task-priority"];
  currentNotes.value = initalTaskData.value["notes"];
  currentCompletionStatus.value = initalTaskData.value["is-completed"];
};

const onSelectEmoji = (emoji) => {
  currentTitle.value = currentTitle.value + emoji["i"];
  showEmojiPicker.value = !showEmojiPicker.value;
};

const toggleEmojiPicker = () => {
  showEmojiPicker.value = !showEmojiPicker.value;
};

const closeTaskDetailSection = () => {
  emit("close-task-details", true);
};

defineExpose({ getActiveTaskItemData });
</script>

<style scoped>
label {
  font-weight: 600;
}

input,
select,
textarea {
  font-family: monospace;
}

.task-details-container {
  position: relative;
  background-color: white;
  padding: 2rem;
  margin-top: 1rem;
  margin-right: 1rem;
  border-radius: 0.5rem;
}

.task-container__elem {
  border-radius: 0.5rem;
  background-color: #efecec;
  width: -webkit-fill-available;
  border: none;
  padding: 0.8rem;
  margin-bottom: 2rem;
  margin-top: 0.5rem;
}

.task-tags {
  display: flex;
  justify-content: start;
  gap: 2rem;
  padding: 0;
  background-color: transparent;
}

.task-tags__priority,
.task-tags__completion {
  padding: 0.2rem 0.8rem;
  border-radius: 0.8rem;
}

.circle-decor {
  color: white;
}

.title-container {
  display: flex;
  position: relative;
}

.task-details__title {
  border-radius: 0.5rem;
  background-color: #efecec;
  width: -webkit-fill-available;
  border: none;
  outline: none;
}

.task-details__notes {
  min-height: 10rem;
  text-align: left;
  line-height: 1rem;
  resize: none;
}

.task-details__right-caret {
  color: #575757;
  transform: scale(2);
  position: absolute;
  inset: 0;
  left: -2%;
  top: 8%;
  cursor: pointer;
}

.create-task__emoji-btn {
  background-color: #cbc9c9;
  border-radius: 0.2rem;
  border: none;
  transform: scale(1.5);
  cursor: pointer;
}

.v3-emoji-picker {
  position: absolute;
  top: 3rem;
  right: -2rem;
}

.task-details__set-btn {
  display: flex;
  gap: 1rem;
}

.task-details__set-btn button {
  font-size: 1rem;
  position: relative;
  margin-top: 0.5rem;
  border-radius: 1.5rem;
  color: white;
  background-color: black;
  cursor: pointer;
  width: -webkit-fill-available;
  border: none;
  padding: 0.8rem;
  z-index: 1;
}

@keyframes swipe {
  0% {
    opacity: 1;
  }
  50% {
    opacity: 0.75;
  }
  100% {
    opacity: 1;
  }
}

.save-btn:hover,
.reset-btn:hover {
  opacity: 0.85;
}

.save-btn:active,
.reset-btn:active {
  animation: swipe 0.5s ease;
}

@media screen and (min-width: 541px) and (max-width: 1024px) {
  .task-details-container {
    padding: 2rem 1rem;
  }
}

@media screen and (max-width: 540px) {
  .task-details-container {
    margin: 0;
  }

  .task-tags {
    justify-content: end;
  }

  .task-details__right-caret {
    left: 2%;
    top: 4%;
  }

  .v3-emoji-picker {
    right: 0;
  }
}
</style>
