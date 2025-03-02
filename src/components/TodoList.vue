<!-- 
=== Template structure ===
    Heading
    Todo lists
    Input field + Emoji button
    Create Todo button
-->

<template>
  <div :class="['todo-list-container', { active: mobileResponsive }]">
    <div class="todo-list__heading">Todo List</div>
    <DisplayListItems
      ref="todoListItems"
      v-model:list-model="todoListData.value"
      listStyles="todo-list-style"
      @get-list-id="emitListId"
      @update-list="updateTodoItemTitle"
      @delete-list="deleteTodoItem"
      ><template #list-icon="{ icon }">
        <span class="todo-list-icon">{{ icon }}</span>
      </template>
      <template #list-count="{ count }">
        <div class="todo-lists__list-count">{{ count }}</div>
      </template>
    </DisplayListItems>
    <div :class="['todo-list__input-container', { active: toShowTodoInput }]">
      <!-- Emoji button -->
      <button @click="toggleTodoEmojiPicker">{{ emojiButtonTxt }}</button
      ><EmojiPicker
        v-if="showTodoEmojiPicker"
        :display-recent="true"
        :native="false"
        @select="onSelectGroupEmoji"
      />
      <input
        type="text"
        maxlength="15"
        v-model="newTodoItem['title']"
        @input="writenewTodoTitle"
        @blur="getnewTodoItem"
      />
    </div>
    <CreateListBtn
      ref="createListBtn"
      btnText="Create new todo"
      class="create-new-todo-btn"
      @toShowModal="toggleTodoInputField"
    />
  </div>
</template>

<script setup>
import { ref, computed, onUnmounted } from "vue";
import EmojiPicker from "vue3-emoji-picker";
import "vue3-emoji-picker/css";
import DisplayListItems from "./DisplayListItems.vue";
import CreateListBtn from "./CreateListBtn.vue";

let mobileResponsive = ref(false);
let todoListItems = ref(null);
let activeEditableTodo = ref(null);
let timerId = ref(null);
let createListBtn = ref(null);
let newTitle = ref(true);
let lastTodoItem = ref({});
const defaultButtonTxt = "😁";
let emojiButtonTxt = ref(defaultButtonTxt);
const defaultTodoObj = {
  icon: emojiButtonTxt.value,
  title: "",
  "task-list": [],
};
let newTodoItem = ref(defaultTodoObj);
let showTodoEmojiPicker = ref(false);
let todoListData = ref([]);
let todoData = defineModel("todo-data");
let toShowTodoInput = ref(false);
let contentEditable = ref(false);

let emit = defineEmits({
  "todo-list-id": (todoListId) => {
    if (typeof todoListId === "number") {
      return true;
    } else {
      console.warn("Invalid Todo List ID!");
      return false;
    }
  },
  "delete-todo-item": (id) => {
    if (typeof id === "number") {
      return true;
    } else {
      console.warn("Invalid, cannot delete todo item");
      return false;
    }
  },
});

lastTodoItem.value = computed(() => todoData[todoData.length - 1]);

todoListData.value = computed(() =>
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

const emitListId = (todoListId, event) => {
  if (event?.target?.className === "options-container") {
    return;
  }
  if (event?.target?.className === "options-button") {
    return;
  } else if (event?.target?.className === "opt-edit") {
    return;
  } else if (event?.target?.className === "opt-delete") {
    return;
  } else if (
    event?.target?.className === "list-name" &&
    contentEditable.value
  ) {
    return;
  }
  todoListItems.value.lists.forEach((list) => {
    list.style.border = "none"; // reset all borders
  });
  todoListItems.value.lists[todoListId].style.border = "1px solid black";

  if (document.body.offsetWidth <= 500) {
    mobileResponsive.value = true;
  }

  emit("todo-list-id", todoListId);
};

const resetTodoInputValues = () => {
  if (showTodoEmojiPicker) {
    showTodoEmojiPicker.value = false;
  }
  newTodoItem["title"] = "";
  newTodoItem.value = { icon: defaultButtonTxt, title: "", "task-list": [] };
  emojiButtonTxt.value = defaultButtonTxt;
  newTitle.value = true;
};
const toggleTodoInputField = () => {
  createListBtn.value.rotateIcon();
  toShowTodoInput.value = !toShowTodoInput.value;
  if (toShowTodoInput) resetTodoInputValues();
};

const restrictTodoTitleLength = (list) => {
  if (list.textContent.length >= 15) {
    contentEditable.value = false;
    list.textContent = list.textContent.slice(0, 14);
  }
};

const saveTodoTitle = (list, key) => {
  list.contentEditable = "false";
  contentEditable.value = false;
  todoData.value[key]["title"] = list.textContent;
};

const updateTodoItemTitle = (key) => {
  if (todoListItems.value) {
    activeEditableTodo.value = todoListItems.value.listTitle[key];
    activeEditableTodo.value.contentEditable = "true";
    contentEditable.value = true;
    activeEditableTodo.value.focus();
    activeEditableTodo.value.addEventListener("keyup", () =>
      restrictTodoTitleLength(activeEditableTodo.value)
    );
    activeEditableTodo.value.addEventListener("focusout", () =>
      saveTodoTitle(activeEditableTodo.value, key)
    );
  }
};

const deleteTodoItem = (id) => {
  emit("delete-todo-item", id);
};

const onSelectGroupEmoji = (emoji) => {
  emojiButtonTxt.value = emoji["i"];
  newTodoItem.value["icon"] = emoji["i"];
  showTodoEmojiPicker.value = !showTodoEmojiPicker.value;
  getnewTodoItem();
};
const toggleTodoEmojiPicker = () => {
  showTodoEmojiPicker.value = !showTodoEmojiPicker.value;
  if (timerId.value !== null) {
    clearTimeout(timerId.value);
    timerId.value = null;
  }
};

// Function to insert new todo item's title
const writenewTodoTitle = (event) => {
  if (event.inputType === "deleteWordBackward" && event.data === null) {
    // deleted all letters at once -> delete last todo item
    if (timerId.value !== null) {
      clearTimeout(timerId.value);
      timerId.value = null;
    }
    todoData.value.pop();
    newTitle.value = true;
  }
  if (newTodoItem.value["title"]) {
    if (newTodoItem.value["title"].length === 1 && newTitle.value) {
      // new todo item => create
      newTitle.value = false;
      todoData.value.push(newTodoItem.value);
    } else if (newTodoItem.value["title"].length === 1 && !newTitle.value) {
      // If not new title but re-writing => update
      lastTodoItem.value["title"] = newTodoItem.value["title"];
    } else if (newTodoItem.value["title"].length > 1) {
      lastTodoItem.value["title"] = newTodoItem.value["title"];
    }
  }
};

// Function to save new todo (title + icon + task-lists) on blur
const getnewTodoItem = () => {
  if (newTodoItem.value["title"]) {
    if (
      newTodoItem.value["icon"] === defaultButtonTxt &&
      !showTodoEmojiPicker.value
    ) {
      if (!timerId.value) {
        // 1200ms window for clicking emoji button after blur
        timerId.value = setTimeout(() => {
          lastTodoItem.value = { ...newTodoItem.value };
          if (toShowTodoInput.value) {
            // Remove and reset input field
            toggleTodoInputField();
          } else {
            // Reset input field
            resetTodoInputValues();
          }
          timerId.value = null;
        }, 1200);
      }
    } else {
      // If emoji picker was clicked
      lastTodoItem.value = { ...newTodoItem.value };
      toggleTodoInputField();
    }
  }
};

onUnmounted(() => {
  if (activeEditableTodo.value) {
    activeEditableTodo.value.removeEventListener(
      "keyup",
      restrictTodoTitleLength
    );
    activeEditableTodo.value.removeEventListener("focusout", saveTodoTitle);
  }
});
</script>

<style scoped>
.todo-list-container {
  position: relative;
  background-color: white;
  padding: 2rem;
  padding-left: 1rem;
  margin: 1rem 0 1rem 1rem;
  border-radius: 0.5rem;
}

.todo-list__heading {
  font-size: 2rem;
  font-weight: 600;
  padding-left: 0.5rem;
  margin-bottom: 1.5rem;
}

::v-deep(li.todo-list-style) {
  padding: 0.5rem;
  margin-bottom: 0.8rem;
  gap: 1rem;
  font-size: 1rem;
  font-weight: 700;
}

::v-deep(div.display-list-container) {
  overflow-y: auto;
  overflow-x: hidden;
  max-height: calc(70vh - 2rem);
  height: calc(70vh - 2rem);
}

::v-deep(div.display-list-container)::-webkit-scrollbar {
  height: 0.5rem;
  width: 0.6rem;
}

::v-deep(div.display-list-container)::-webkit-scrollbar-thumb {
  border-radius: 0.5rem;
  background-color: rgba(167, 163, 163, 0.707);
}

::v-deep(li.todo-list-style:hover) {
  background-color: #efecec;
}

.todo-lists__list-count {
  border-radius: 50%;
  padding: 0rem 0.6rem;
  background-color: #e1e1e1;
  margin-left: auto;
}

.todo-list__input-container {
  display: flex;
  gap: 0.2rem;
  margin: 1rem 0;
  width: calc(100% - 2rem);
  position: absolute;
  max-height: 0;
  bottom: -50%;
  transition: max-height 0.8s ease-in-out;
}

.todo-list__input-container.active {
  bottom: 8%;
  max-height: 200px;
}

.todo-list__input-container button {
  border-radius: 0.5rem;
  border: 1px solid black;
  width: 2rem;
  font-size: 1.2rem;
  cursor: pointer;
}

.todo-list__input-container input {
  border-radius: 0.5rem;
  padding: 0.5rem;
  background-color: #efecec;
  width: -webkit-fill-available;
  border: 1px solid black;
  outline: none;
}

::v-deep(button.create-new-todo-btn) {
  position: absolute;
  bottom: 2%;
  width: calc(100% - 2rem);
  color: rgb(0, 0, 0);
  background: #efecec;
  text-align: left;
  border: 1px solid rgb(96, 96, 96);
  font-weight: 500;
}

.todo-list-icon {
  transform: scale(1.3);
}

.v3-emoji-picker {
  position: absolute;
  bottom: 100%;
  z-index: 5;
}

@media screen and (min-width: 541px) and (max-width: 1024px) {
  .todo-list-container {
    padding-left: 1rem;
    padding-right: 1rem;
  }
}

@media screen and (max-width: 540px) {
  .todo-list-container {
    margin: 0;
  }
}
</style>
