<template>
  <base-container>
    <h2>Active User</h2>
    <base-search @search="updateSearch" :search-term="enteredSearchTerm"></base-search>
    <div>
      <button @click="sort('asc')" :class="{selected: sorting === 'asc'}">Sort Ascending</button>
      <button @click="sort('desc')" :class="{selected: sorting === 'desc'}">Sort Descending</button>
    </div>
    <ul>
      <user-item v-for="user in users" :key="user.id" :user-name="user.name" :id="user.id" ></user-item>
    </ul>
  </base-container>
</template>

<script setup lang="ts">
import { ref, computed, watch } from 'vue';
import { type User } from './user'
import UserItem from './UserItem.vue';

const enteredSearchTerm = ref('');
const activeSearchTerm = ref('');

type Mode = 'desc' | 'asc';
const sorting = ref<Mode>();

const props = defineProps<{ users: User[] }>();
const availableUsers = computed(() => {
  let resultUsers: User[] = [];
  if (activeSearchTerm.value) {
    resultUsers = props.users.filter((user) => {
      return user.name.includes(activeSearchTerm.value)
    })
  } else if (props.users) {
    resultUsers = props.users;
  }
  return resultUsers;
})
const displayedUsers = computed(() => {
  if (!sorting.value) {
    return availableUsers
  }
  return availableUsers.value.slice().sort((u1, u2) => {
    if (sorting.value === 'asc' && u1.name > u2.name) {
      return 1;
    } else if (sorting.value === 'asc') {
      return -1;
    } else if (sorting.value === 'desc' && u1.name < u2.name) {
      return 1;
    } else if (sorting.value === 'desc') {
      return -1
    } else {
      return 1
    }
  })
})

function updateSearch(val: any) {
  enteredSearchTerm.value = val;
}
function sort(mode: Mode) {
  sorting.value = mode;
}

watch(enteredSearchTerm, (oldVal, newVal) => {
  setTimeout(() => {
    if (enteredSearchTerm.value === newVal) {
      enteredSearchTerm.value = newVal;
    }
  }, 300);
})
</script>