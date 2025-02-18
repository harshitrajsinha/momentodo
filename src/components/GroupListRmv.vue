<template>
  <div class="group-list-container">
    <ul>
      <li
        v-for="(groupList, index) in groupListModel"
        :key="index"
        class="group-lists"
      >
        <span>{{ groupList.icon }}</span>
        <span>{{ groupList.title }}</span>
      </li>
    </ul>
  </div>
</template>
<script setup>
const groupListModel = defineModel("group-list");
</script>

<style scoped>
.group-lists {
  padding: 0.5rem;
  padding-left: 0;
  margin-bottom: 0.5rem;
  border-radius: 0.5rem;
  display: flex;
  gap: 1rem;
  align-items: baseline;
  font-size: 1rem;
  cursor: pointer;
}

.group-lists:hover {
  background-color: #efecec;
}
</style>
