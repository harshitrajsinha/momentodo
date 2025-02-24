<template>
  <button :class="$attrs.class" class="create-list-btn" @click="handleBtnClick">
    <font-awesome-icon
      :style="{
        transform: `rotate(${rotation}deg)`,
        transition: `transform 1s`,
        marginRight: `5px`,
      }"
      :icon="['fas', 'plus']"
    />
    {{ btnText }}
  </button>
</template>

<script setup>
import { ref } from "vue";
let rotation = ref(90);
defineProps({ btnText: String });
const emit = defineEmits(["toShowModal"]);

const handleBtnClick = () => {
  emit("toShowModal");
};

const rotateIcon = () => {
  rotation.value = 90 - rotation.value;
};
defineExpose({ rotateIcon });
</script>

<style scoped>
.create-list-btn {
  margin-top: 0.5rem;
  font-size: 1rem;
  border-radius: 1.5rem;
  color: white;
  background-color: black;
  cursor: pointer;
  border: none;
  padding: 0.6rem 0.5rem;
  z-index: 1;
}
</style>
