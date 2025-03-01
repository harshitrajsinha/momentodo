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
      @click="closeModal"
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
      <button
        :class="['save-btn', { active: animateSaveBgClr }]"
        @click="saveData"
      >
        Save
      </button>
      <button
        :class="['reset-btn', { active: animateResetBgClr }]"
        @click="resetData"
      >
        Reset
      </button>
    </div>
  </div>
</template>

<script setup>
import EmojiPicker from "vue3-emoji-picker";
import "vue3-emoji-picker/css";
import { ref, defineEmits } from "vue";

let showEmojiPicker = ref(false);
let currentListId = ref(-1);
let currentTitle = ref("");
let currentPriority = ref("");
let currentNotes = ref("");
let currentCompletionStatus = ref(false);
let initalTaskData = ref({});
let allData = defineModel("allTaskData");
let animateSaveBgClr = ref(false);
let animateResetBgClr = ref(false);
let timerId = ref(null);

const getCurrentListData = (data) => {
  currentListId.value = data;
  initalTaskData.value = allData.value[data];
  currentTitle.value = allData.value[data]["title"];
  currentPriority.value = allData.value[data]["task-priority"];
  currentNotes.value = allData.value[data]["notes"];
  currentCompletionStatus.value = allData.value[data]["is-completed"];
};

const saveData = () => {
  if (!animateSaveBgClr.value) {
    animateSaveBgClr.value = true;
    if (!timerId.value) {
      timerId.value = setTimeout(() => {
        animateSaveBgClr.value = false;
        timerId.value = null;
      }, 600);
    }
  }
  allData.value[currentListId.value]["title"] = currentTitle.value;
  allData.value[currentListId.value]["task-priority"] = currentPriority.value;
  allData.value[currentListId.value]["notes"] = currentNotes.value;
};

const resetData = () => {
  if (!animateResetBgClr.value) {
    animateResetBgClr.value = true;
    if (!timerId.value) {
      timerId.value = setTimeout(() => {
        animateResetBgClr.value = false;
        timerId.value = null;
      }, 600);
    }
  }
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

const emit = defineEmits(["close-modal"]);

const closeModal = () => {
  emit("close-modal", false);
};

defineExpose({ getCurrentListData });
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

@keyframes blink {
  0% {
    transform: scale(1.5);
  }
  50% {
    transform: scale(2);
  }
  100% {
    transform: scale(1.5);
  }
}

/* .task-details__right-caret:hover {
  animation: blink 2s infinite;
} */

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

.save-btn.active,
.reset-btn.active {
  animation: swipe 0.5s ease;
}
</style>
