<template>
  <div class="user-table__modal-overlay">
    <div class="user-table__modal">
      <h3 class="user-table__modal-title">Добавление пользователя</h3>
      <form class="user-table__form"
            @submit.prevent="submitForm">
        <input class="user-table__input"
               v-model="form.name"
               placeholder="Имя" required />
        <input class="user-table__input"
               v-model="form.phone"
               placeholder="+7 (___) ___-__-__"
               v-maska
               data-maska="+7 (###) ###-##-##"
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
                @click.prevent="$emit('close')">
        Закрыть
        </button>
      </form>
    </div>
  </div>
</template>

<script setup>
import { reactive } from 'vue'
import { vMaska } from 'maska'

defineProps({
  users: Array,
})


const emit = defineEmits(['add-user', 'close'])

const form = reactive({
  name: '',
  phone: '',
  parentId: null,
})

const submitForm = ()=> {
  emit('add-user', {
    id: Date.now(),
    name: form.name,
    phone: form.phone,
    parentId: form.parentId,
  })

  form.name = ''
  form.phone = ''
  form.parentId = null
  emit('close')
}
</script>

<style scoped>
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

.user-table__btn--cancel {
  background-color: #ccc;
  color: #333;
}

.user-table__btn--cancel:hover {
  background-color: #bbb;
}

</style>