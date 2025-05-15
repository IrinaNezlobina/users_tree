<template>
  <div class="user-table__row">
    <div
      class="user-table__cell"
      :style="{ paddingLeft: `${level * 20}px` }"
    >
      <span v-if="user.children?.length" @click="toggleOpen" class="user-table__toggle">
        {{ isOpen ? '−' : '+' }}
      </span>
      {{ user.name }}
    </div>
    <div class="user-table__cell">{{ user.phone }}</div>
  </div>

  <div v-if="isOpen">
    <UserRow
      v-for="child in user.children"
      :key="child.id"
      :user="child"
      :users="users"
      :level="level + 1"
    />
  </div>
</template>

<script setup>
import { ref } from 'vue'

defineProps(['user', 'users', 'level'])


const isOpen = ref(true)

const toggleOpen = ()=> {
  isOpen.value = !isOpen.value
}
</script>

<style scoped>
.user-table__toggle {
  cursor: pointer;
  font-weight: bold;
  margin-right: 6px;
  display: inline-block;
  width: 16px;
  text-align: center;
}

.user-table__cell {
  flex: 1;
  padding: 0 8px;
}
.user-table__row {
  display: flex;
  padding: 12px 16px;
  border-bottom: 1px solid #eaeaea;
}

</style>
