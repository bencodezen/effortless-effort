<script setup>
import { ref } from 'vue'
import { useStorage } from '@vueuse/core'
import TaskCard from './components/TaskCard.vue'

const newTaskTitle = ref('')
const taskList = useStorage('task-list', [])

const addTask = () => {
  taskList.value.push({
    title: newTaskTitle.value,
    estimate: 0,
    remaining: 0,
    completion: 0
  })
  newTaskTitle.value = ''
}
</script>

<template>
  <main class="container">
    <h1 style="margin-top: 20px">Effortless Estimates</h1>
    <h2>New Task</h2>
    <form @submit.prevent>
      <label for="new-task-title">
        New Task Title
        <input
          type="text"
          id="new-task-title"
          name="new-task-title"
          placeholder="Placeholder task title"
          v-model="newTaskTitle"
          @keyup.enter="addTask"
        />
      </label>
    </form>
    <h2>Tasks</h2>
    <ul class="task-list">
      <li
        v-for="(task, index) in taskList"
        :key="`task-${index}`"
        class="task-list-item"
      >
        <TaskCard :task="task" />
      </li>
    </ul>
  </main>
</template>

<style scoped>
h1 {
  --typography-spacing-vertical: 20px;
}

h2 {
  --typography-spacing-vertical: 10px;
}

.task-list {
  padding: 0;
}
.task-list-item {
  list-style: none;
}
</style>
