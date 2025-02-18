<template>
  <div>
    <DisplayItemList
      ref="displayList"
      v-model:list-model="taskLists"
      listStyles="task-list-style"
      listContainerStyle="list-container-style"
      @getListId="getClickedListId"
    >
      <!-- slot component -->
      <template #list-checkbox="{ completionStatus, indexVal }">
        <input
          class="task-lists__is-completed"
          type="checkbox"
          :checked="completionStatus"
          @click="toggleCheckbox(indexVal)"
        />
      </template>
    </DisplayItemList>

    <CreateList v-model="taskLists" />
  </div>
</template>

<script setup>
import DisplayItemList from "./DisplayItemList.vue";
import CreateList from "./CreateList.vue";

const taskLists = defineModel("task-list-data");
const emit = defineEmits(["getListId"]);

const getClickedListId = (id) => {
  emit("getListId", id);
};

const toggleCheckbox = (id) => {
  const isCompleted = taskLists.value[id]["is-completed"];
  taskLists.value[id]["is-completed"] = !isCompleted;
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
</style>
