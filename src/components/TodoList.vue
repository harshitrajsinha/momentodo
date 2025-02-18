<template>
  <div class="todo-list-container">
    <div class="todo-list__heading">Lists</div>
    <!-- Pass a default icon in case icon is not present -->
    <DisplayItemList
      v-model:list-model="listItems.value"
      listStyles="todo-list-style"
      @getListId="handleListId"
      ><template #list-icon="{ icon }">
        <span>{{ icon }}</span>
      </template>
    </DisplayItemList>
    <div :class="['todo-list__input', { active: toShowTodoInput }]">
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
    <CreateListBtn
      btnText="Create new list"
      class="create-list-btn-style"
      @toShowModal="toggleTodoListInput"
    />
  </div>
</template>

<script setup>
import EmojiPicker from "vue3-emoji-picker";
import "vue3-emoji-picker/css";
import DisplayItemList from "./DisplayItemList.vue";
import CreateListBtn from "./CreateListBtn.vue";
import { ref, computed } from "vue";

const defaultButtonTxt = "😁";
let buttonTxt = ref(defaultButtonTxt);
let newTodo = ref({ icon: buttonTxt.value, title: "" });
let showTodoEmojiPicker = ref(false);
let todoListData = defineModel("todo-list-data");
let listItems = ref([]);
let toShowTodoInput = ref(false);

listItems.value = computed(() =>
  todoListData.value.map((elem) => {
    return { title: elem["title"], icon: elem["icon"] };
  })
);

const handleListId = (id) => {
  console.log(id);
};

const toggleTodoListInput = () => {
  console.log("button clicked");
  toShowTodoInput.value = !toShowTodoInput.value;
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

.todo-list__input {
  display: flex;
  gap: 0.2rem;
  margin-top: 1rem;
  position: relative;
  bottom: -100vh;
  transition: bottom 1.5s ease-in-out;
}

.todo-list__input.active {
  position: relative;
  bottom: 0;
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

::v-deep(button.create-list-btn-style) {
  position: absolute;
  width: calc(100% - 4rem);
  bottom: 2%;
}

.v3-emoji-picker {
  position: absolute;
  top: 2.5rem;
  z-index: 2;
}
</style>
