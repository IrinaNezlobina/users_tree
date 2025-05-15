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

    <div v-if="showModal" class="user-table__modal-overlay">
      <div class="user-table__modal">
        <h3 class="user-table__modal-title">Добавление пользователя</h3>
        <form class="user-table__form"
              @submit.prevent="addUser">
          <input class="user-table__input"
                 v-model="form.name"
                 placeholder="Имя" required />
          <input class="user-table__input"
                 v-model="form.phone"
                 placeholder="+7 (___) ___-__-__"
                 v-maska="'+7 (###) ###-##-##'"
                 required />
          <select class="user-table__input"
                  v-model="form.parentId">
            <option :value="null">Без начальника</option>
            <option v-for="user in users"
                    :key="user.id"
                    :value="user.id">
              {{ user.name }}
            </option>
          </select>
          <button class="user-table__btn" type="submit">Сохранить</button>
          <button class="user-table__btn user-table__btn--cancel"
                  @click.prevent="showModal = false">Закрыть</button>
        </form>
      </div>
    </div>
  </div>
</template>


<script setup>
import { ref, computed, watch } from "vue";
import UserRow from "../components/UserRow";

// data
const showModal = ref(false);
const users = ref(JSON.parse(localStorage.getItem("users") || "[]"));
const form = ref({
  name: "",
  phone: "",
  parentId: null
});
const sortKey = ref("name");
const sortOrder = ref(1);
// watch
watch(users, (newVal) => {
  localStorage.setItem("users", JSON.stringify(newVal));
}, { deep: true });
// methods
const addUser = () => {
  users.value.push({
    id: Date.now(),
    name: form.value.name,
    phone: form.value.phone,
    parentId: form.value.parentId
  });
  form.value = { name: "", phone: "", parentId: null };
  showModal.value = false;
};

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

.user-table__modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.3);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 10;
}

.user-table__modal {
  background: white;
  padding: 24px;
  border-radius: 10px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  width: 320px;
}

.user-table__modal-title {
  margin-top: 0;
  margin-bottom: 12px;
  font-size: 18px;
}

.user-table__form {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.user-table__input {
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 6px;
  font-size: 14px;
  outline: none;
  transition: border-color 0.2s;
}

.user-table__input:focus {
  border-color: #28a745;
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

.user-table__btn--cancel {
  background-color: #ccc;
  color: #333;
}

.user-table__btn--cancel:hover {
  background-color: #bbb;
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