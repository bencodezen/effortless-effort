<script setup>
import { ref } from 'vue'
import { useStorage } from '@vueuse/core'
import minutesToSeconds from 'date-fns/minutesToSeconds'
import intervalToDuration from 'date-fns/intervalToDuration'

const newTaskTitle = ref('')
const taskList = useStorage('task-list', [])
const intervalId = ref('')

const addTask = () => {
  taskList.value.push({
    title: newTaskTitle.value,
    estimate: 0,
    remaining: 0
  })
  newTaskTitle.value = ''
}

const generateTimeStamp = task => {
  const remainingTimeLabel = intervalToDuration({
    start: 0,
    end: task.remaining * 1000
  })

  return `${remainingTimeLabel.hours}h ${remainingTimeLabel.minutes}m ${remainingTimeLabel.seconds}s`
}

const startTimer = task => {
  if (!task.remaining) {
    task.remaining = minutesToSeconds(task.estimate)
  }

  intervalId.value = setInterval(() => {
    task.remaining--
  }, 1000)
}

const stopTimer = () => {
  clearInterval(intervalId.value)
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
        <article class="task-card">
          <p style="flex: 1; margin: 0">{{ task.title }}</p>
          <div>
            <div style="margin-bottom: 15px">
              <input
                type="number"
                style="width: 80px; margin: 0; margin-right: 10px"
                v-model="task.estimate"
              />
              min
            </div>
            <div style="margin-bottom: 15px">
              <strong>Remaining Time</strong>:
              {{ generateTimeStamp(task) }}
            </div>
            <button @click="startTimer(task)">Start</button>
            <button @click="stopTimer" class="secondary">Stop</button>
          </div>
        </article>
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

.task-card {
  display: flex;
  align-items: center;
}
.task-list {
  padding: 0;
}
.task-list-item {
  list-style: none;
}
</style>
