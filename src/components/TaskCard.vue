<script setup>
import { computed, ref } from 'vue'
import minutesToSeconds from 'date-fns/minutesToSeconds'
import intervalToDuration from 'date-fns/intervalToDuration'

const props = defineProps({
  task: Object
})

const intervalId = ref('')

const generateTimeStamp = computed(() => {
  const remainingTimeLabel = intervalToDuration({
    start: 0,
    end: props.task.remaining * 1000
  })

  return `${remainingTimeLabel.hours}h ${remainingTimeLabel.minutes}m ${remainingTimeLabel.seconds}s`
})

const startTimer = () => {
  if (!props.task.remaining) {
    props.task.remaining = minutesToSeconds(props.task.estimate)
  }

  intervalId.value = setInterval(() => {
    props.task.remaining--
  }, 1000)
}

const stopTimer = () => {
  clearInterval(intervalId.value)
}
</script>

<template>
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
        {{ generateTimeStamp }}
      </div>
      <button @click="startTimer">Start</button>
      <button @click="stopTimer" class="secondary">Stop</button>
    </div>
  </article>
</template>

<style scoped>
.task-card {
  display: flex;
  align-items: center;
}
</style>
