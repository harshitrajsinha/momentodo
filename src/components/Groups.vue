<template>
  <div class="group-container">
    <div class="create-list__list">
      <div class="heading">Lists</div>
      <GroupList v-model:group-list="groupList" />
      <div class="create-list__input">
        <button @click="toggleGroupEmojiPicker">{{ buttonTxt }}</button
        ><EmojiPicker
          v-if="showGroupEmojiPicker"
          :display-recent="true"
          :native="false"
          @select="onSelectGroupEmoji"
        />
        <input
          type="text"
          maxlength="25"
          v-model="newGroupObj['title']"
          @blur="updateObj"
        />
      </div>
    </div>
    <button class="create-list-container__create-btn" @click="showAddList">
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
import GroupList from "./GroupList.vue";
import { ref } from "vue";
const defaultButtonTxt = "😁";
let buttonTxt = ref(defaultButtonTxt);
let newGroupObj = ref({ icon: buttonTxt.value, title: "" });
let showGroupEmojiPicker = ref(false);
let groupList = ref([
  { icon: "😁", title: "Home" },
  { icon: "😁", title: "Tech" },
]);
let rotation = ref(90);
const showAddList = () => {
  rotation.value = 90 - rotation.value;
};
const onSelectGroupEmoji = (emoji) => {
  buttonTxt.value = emoji["i"];
  newGroupObj.value["icon"] = emoji["i"];
  showGroupEmojiPicker.value = !showGroupEmojiPicker.value;
};
const toggleGroupEmojiPicker = () => {
  showGroupEmojiPicker.value = !showGroupEmojiPicker.value;
};

const updateObj = () => {
  if (newGroupObj.value["title"]) groupList.value.push(newGroupObj.value);
  // buttonTxt.value = defaultButtonTxt;
};
</script>

<style scoped>
.group-container {
  position: relative;
  background-color: white;
  padding: 2rem;
  margin-top: 1rem;
  margin-bottom: 1rem;
  margin-left: 1rem;
  border-radius: 0.5rem;
}

.heading {
  font-size: 2rem;
  font-weight: 600;
  margin-bottom: 1.5rem;
}

.create-list__input {
  display: flex;
  gap: 0.2rem;
  margin-top: 1rem;
  position: relative;
}

.create-list__input button {
  border-radius: 0.5rem;
  border: 1px solid black;
  width: 2rem;
  font-size: 1.2rem;
  cursor: pointer;
}

.create-list__input input {
  border-radius: 0.5rem;
  padding: 0.5rem;
  background-color: #efecec;
  width: -webkit-fill-available;
  border: 1px solid black;
  outline: none;
}

.create-list-container__create-btn {
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
