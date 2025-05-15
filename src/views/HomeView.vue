<template>
  <div class="user-table">
    <div class="user-table__btn-wrapper">
      <button class="user-table__btn"
              @click="showModal = true">Добавить</button>
    </div>

    <div class="user-table__table">
      <div class="user-table__header">
        <div class="user-table__cell user-table__cell--clickable"
             @click="sortBy('name')">Имя</div>
        <div class="user-table__cell user-table__cell--clickable"
             @click="sortBy('phone')">Телефон</div>
      </div>

      <div class="user-table__body">
        <template v-for="user in treeUsers"
                  :key="user.id">
          <UserRow :user="user"
                   :users="users"
                   :level="0" />
        </template>
      </div>
    </div>

    <UserModal v-if="showModal" :users="users" @add-user="addUser" @close="showModal = false" />

  </div>
</template>


<script setup>
import { ref, computed, watch } from "vue";
import UserRow from "../components/UserRow";
import UserModal from "../components/UserModal";

// data
const showModal = ref(false);
const users = ref(JSON.parse(localStorage.getItem("users") || "[]"));
const sortKey = ref("name");
const sortOrder = ref(1);
// watch
watch(users, (newVal) => {
  localStorage.setItem("users", JSON.stringify(newVal));
}, { deep: true });
// methods
const addUser = (user) => {
  users.value.push(user)
}

const sortBy = (key) => {
  if (sortKey.value === key) {
    sortOrder.value *= -1;
  } else {
    sortKey.value = key;
    sortOrder.value = 1;
  }
};

const buildTree = (list, parentId = null) => {
  return list
    .filter(u => u.parentId === parentId)
    .sort((a, b) => {
      if (a[sortKey.value] > b[sortKey.value]) return sortOrder.value;
      if (a[sortKey.value] < b[sortKey.value]) return -sortOrder.value;
      return 0;
    })
    .map(u => ({
      ...u,
      children: buildTree(list, u.id)
    }));
};

const treeUsers = computed(() => buildTree(users.value));
</script>

<style scoped>
.user-table {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  max-width: 800px;
  margin-inline: auto;
}

.user-table__header {
  display: flex;
  background: #f9f9f9;
  font-weight: 600;
  border-bottom: 1px solid #eaeaea;
}

.user-table__body {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  overflow: hidden;
}

.user-table__cell {
  flex: 1;
  padding: 12px 16px;
}

.user-table__cell--clickable {
  cursor: pointer;
  user-select: none;
  transition: background-color 0.2s;
}

.user-table__cell--clickable:hover {
  background-color: #eef9f0;
}

.user-table__btn {
  background-color: #28a745;
  color: white;
  border: none;
  padding: 8px;
  border-radius: 6px;
  cursor: pointer;
  font-weight: bold;
  transition: .4s;
}

.user-table__btn:hover {
  background-color: #218838;
}

.user-table__btn-wrapper {
  text-align: right;
  margin-bottom: 30px;
}

.user-table__cell--clickable {
  cursor: pointer;
  user-select: none;
  transition: background-color 0.2s;
}

.user-table__cell--clickable:hover {
  background-color: #eef9f0;
}
</style>