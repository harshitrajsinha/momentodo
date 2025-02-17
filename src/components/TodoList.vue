<template>
  <div class="todo-list-container">
    <div class="todo-list__heading">Lists</div>
    <DisplayList
      v-model:list-model="listItems.value.value"
      listStyles="todo-list-style"
      @getListId="handleListId"
      ><template #list-icon="{ icon }">
        <span>{{ icon }}</span>
      </template>
    </DisplayList>
    <div class="todo-list__input">
      <button @click="toggleTodoEmojiPicker">{{ buttonTxt }}</button
      ><EmojiPicker
        v-if="showTodoEmojiPicker"
        :display-recent="true"
        :native="false"
        @select="onSelectGroupEmoji"
      />
      <input
        type="text"
        maxlength="25"
        v-model="newTodo['title']"
        @blur="getNewTodo"
      />
    </div>
    <button class="todo-list-container__create-btn" @click="showBtn">
      <font-awesome-icon
        :style="{
          transform: `rotate(${rotation}deg)`,
          transition: `transform 1s`,
          marginRight: `5px`,
        }"
        :icon="['fas', 'plus']"
      />
      Create new list
    </button>
  </div>
</template>

<script setup>
import EmojiPicker from "vue3-emoji-picker";
import "vue3-emoji-picker/css";
import DisplayList from "./DisplayList.vue";
import { ref, computed } from "vue";

const defaultButtonTxt = "😁";
let buttonTxt = ref(defaultButtonTxt);
let newTodo = ref({ icon: buttonTxt.value, title: "" });
let showTodoEmojiPicker = ref(false);
let todoListData = defineModel("todo-list-data");
let listItems = [];

listItems.value = computed(() =>
  todoListData.value.map((elem) => {
    return { title: elem["title"], icon: elem["icon"] };
  })
);

const handleListId = (id) => {
  console.log(id);
};

let rotation = ref(90);
const showBtn = () => {
  rotation.value = 90 - rotation.value;
};
const onSelectGroupEmoji = (emoji) => {
  buttonTxt.value = emoji["i"];
  newTodo.value["icon"] = emoji["i"];
  showTodoEmojiPicker.value = !showTodoEmojiPicker.value;
};
const toggleTodoEmojiPicker = () => {
  showTodoEmojiPicker.value = !showTodoEmojiPicker.value;
};

const getNewTodo = () => {
  if (newTodo.value["title"]) todoListData.value.push(newTodo.value);
};
</script>

<style scoped>
::v-deep(li.todo-list-style) {
  padding: 0.5rem;
  padding-left: 0;
  margin-bottom: 0.5rem;
  gap: 1rem;
  font-size: 1rem;
}

::v-deep(li.todo-list-style:hover) {
  background-color: #efecec;
}

.todo-list-container {
  position: relative;
  background-color: white;
  padding: 2rem;
  margin-top: 1rem;
  margin-bottom: 1rem;
  margin-left: 1rem;
  border-radius: 0.5rem;
}

.todo-list__heading {
  font-size: 2rem;
  font-weight: 600;
  margin-bottom: 1.5rem;
}

.todo-list__input {
  display: flex;
  gap: 0.2rem;
  margin-top: 1rem;
  position: relative;
}

.todo-list__input button {
  border-radius: 0.5rem;
  border: 1px solid black;
  width: 2rem;
  font-size: 1.2rem;
  cursor: pointer;
}

.todo-list__input input {
  border-radius: 0.5rem;
  padding: 0.5rem;
  background-color: #efecec;
  width: -webkit-fill-available;
  border: 1px solid black;
  outline: none;
}

.todo-list-container__create-btn {
  font-size: 1rem;
  position: absolute;
  bottom: 2%;
  margin-top: 0.5rem;
  border-radius: 1.5rem;
  color: white;
  background-color: black;
  cursor: pointer;
  width: calc(100% - 4rem);
  border: none;
  padding: 0.8rem;
  z-index: 1;
}

.v3-emoji-picker {
  position: absolute;
  top: 2.5rem;
  z-index: 2;
}
</style>
