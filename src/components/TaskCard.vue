<script setup>
import { computed, ref } from 'vue'
import minutesToSeconds from 'date-fns/minutesToSeconds'
import intervalToDuration from 'date-fns/intervalToDuration'

const props = defineProps({
  task: Object
})

const intervalId = ref('')

/**
 * Computed Properties
 */
const displayTimeText = computed(() => {
  if (props.task.remaining >= 0) {
    return 'Time Remaining'
  } else {
    return 'Additional Time Spent'
  }
})

const estimateAnalysis = computed(() => {
  if (props.task.completion && props.task.estimate) {
    const timeDiff = props.task.completion - props.task.estimate
    const timeDiffDetails = intervalToDuration({
      start: 0,
      end: timeDiff * 1000
    })

    return `${timeDiffDetails.hours}h ${timeDiffDetails.minutes}m ${timeDiffDetails.seconds}s`
  } else {
    return false
  }
})

const generateTimeStamp = computed(() => {
  const remainingTimeLabel = intervalToDuration({
    start: 0,
    end: props.task.remaining * 1000
  })

  return `${remainingTimeLabel.hours}h ${remainingTimeLabel.minutes}m ${remainingTimeLabel.seconds}s`
})

/**
 * Methods
 */
const completeTask = () => {
  props.task.completion = props.task.remaining
}

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
    <div style="flex: 1">
      <p style="flex: 1; margin: 0; font-size: 1.5rem; font-weight: bold">
        {{ task.title }}
      </p>
      <p v-if="task.completion">Completed: {{ task.completion }}</p>
      <p
        v-if="estimateAnalysis"
        :style="`color: ${task.completion > 0 ? 'green' : 'red'}`"
      >
        Status: {{ estimateAnalysis }}
      </p>
    </div>
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
        <strong>{{ displayTimeText }}</strong
        >:
        {{ generateTimeStamp }}
      </div>
      <button @click="startTimer">Start</button>
      <button @click="stopTimer" class="secondary">Stop</button>
      <button @click="completeTask" class="contrast">Complete</button>
    </div>
  </article>
</template>

<style scoped>
.task-card {
  display: flex;
  align-items: center;
}
</style>
