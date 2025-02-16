<template>
  <div class="task-details-container">
    <!-- <div class="task-completed"></div> -->
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
let initalTaskData = ref({});
const getCurrentListData = (data) => {
  initalTaskData.value = data;
  currentTitle.value = data["title"];
  currentPriority.value = data["task-priority"];
  currentNotes.value = data["notes"];
};

const emit = defineEmits(["close-modal"]);

const closeModal = () => {
  emit("close-modal", false);
};

defineExpose({ getCurrentListData });
</script>

<style scoped>
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

.task-details__notes {
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

.task-details__right-caret:hover {
  animation: blink 2s infinite;
}

.task-details__right-caret {
  color: #575757;
  /* font-size: 2rem; */
  transform: scale(2);
  position: absolute;
  inset: 0;
  left: -2%;
  top: 8%;
  cursor: pointer;
}
</style>
