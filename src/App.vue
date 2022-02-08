<script setup>
import { ref } from 'vue'

const newTaskTitle = ref('')
const taskList = ref([])

const addTask = () => {
  taskList.value.push({
    title: newTaskTitle.value
  })
  newTaskTitle.value = ''
}
</script>

<template>
  <main class="container">
    <h1>Effortless Estimates</h1>
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
    <ul>
      <li v-for="(task, index) in taskList" :key="`task-${index}`">
        {{ task.title }}
      </li>
    </ul>
  </main>
</template>

<style></style>
