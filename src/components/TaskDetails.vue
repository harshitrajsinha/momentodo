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
      >Task Title
      <input
        class="task-container__elem task-details__title"
        type="'text'"
        placeholder="Edit title"
        v-model="currentTitle"
      />
    </label>
    <label
      >Task priority
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
      >Task notes
      <textarea
        class="task-container__elem task-details__notes"
        placeholder="Edit notes"
        v-model="currentNotes"
      ></textarea>
    </label>
  </div>
</template>

<script setup>
import { ref, defineEmits } from "vue";

let currentTitle = ref("");
let currentPriority = ref("");
let currentNotes = ref("");
let currentCompletionStatus = ref(false);
let initalTaskData = ref({});
const getCurrentListData = (data) => {
  initalTaskData.value = data;
  currentTitle.value = data["title"];
  currentPriority.value = data["task-priority"];
  currentNotes.value = data["notes"];
  currentCompletionStatus.value = data["is-completed"];
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

  /* width: 30%;
  max-width: 30%; */
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

.task-details__notes {
  min-height: 5rem;
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
</style>
