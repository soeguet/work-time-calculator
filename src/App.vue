<script setup lang="ts">
import { ref, type Ref } from 'vue'

const start: Ref<string> = ref('')
const end: Ref<string> = ref('')
let breakThirty: boolean = false
let breakFifteen: boolean = false
const breakOption: Ref<boolean> = ref(true)
const customBreak: Ref<boolean> = ref(false)
const breakTime: Ref<number> = ref(0)

function time(): string {
  let startTime: Date = new Date(start.value)
  let endTime: Date = new Date(end.value)
  let total: number = endTime.getTime() - startTime.getTime()

  if (isNaN(total)) {
    return '00:00'
  } else if (total < 0) {
    return 'start is after end!'
  }

  if (breakOption.value) {
    total = breakNeeded(total)
  } else {
    breakFifteen = false
    breakThirty = false
  }

  let hours: number = Math.floor(total / 1000 / 60 / 60)
  let minutes: number = (total / 1000 / 60) % 60

  return hours.toString().padStart(2, '0') + ':' + minutes.toString().padStart(2, '0')
}

function breakNeeded(total: number): number {
  //if work for over 9 hours
  if (total > 32400000) {
    breakFifteen = true
    breakThirty = true
    return total - 2700000
  }
  //if work for over 6 hours
  if (total > 21600000) {
    breakFifteen = false
    breakThirty = true
    return total - 1800000
  }

  breakFifteen = false
  breakThirty = false
  return total
}
</script>

<template>
  <div class="main">
    <div class="container">
      <dialog id="options">
        <h2>Options</h2>

        <div id="settings">
          <label for="checkboxBreak">evaluate breaks: </label>
          <input type="checkbox" id="checkboxBreak" v-model="breakOption" />
        </div>
        <div id="settings">
          <label for="checkboxCustom">custom break time:</label>
          <input type="checkbox" id="checkboxCustom" v-model="customBreak" />
        </div>
        <button onclick="options.close()">Close</button>
      </dialog>
      <div class="gear"></div>
      <div class="title">
        <h1>Work Time Calculator</h1>
        <button type="button" class="gear-button" onclick="options.showModal()">⚙</button>
      </div>
      <div class="inner">
        <label for="start">Start:</label>
        <input type="datetime-local" v-model="start" id="start" class="time" />
      </div>
      <div class="inner">
        <label for="end">End:</label>
        <input type="datetime-local" v-model="end" id="end" class="time" />
      </div>

      <div v-if="customBreak" class="inner break">
        <label for="breakTime">Custom break time:</label>
        <input type="number" v-model="breakTime" id="breakTime" />
      </div>

      <div class="inner total">
        <div>Work Time:</div>
        <div>
          {{ time() }}
        </div>
        <div id="explanation inner">
          <p v-if="breakFifteen">Work time exceeds 9 hours. 45 Minute break subtracted!</p>
          <p v-else-if="breakThirty">Work time exceeds 6 hours. 30 Minute break subtracted!</p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.main {
  display: grid;
  height: 80vh;
  justify-content: center;
}

.title {
  margin-bottom: 10px;
  display: flex;
  justify-content: space-between;
}

.container {
  border-style: dashed;
  display: grid;
  margin: auto;
  width: 600px;
  height: 400px;
  border-radius: 50px;
  padding-block: 70px;
  padding-inline: 50px;
  background-color: var(--color-background-mute);
}

#explanation {
  overflow: scroll;
}

.inner {
  display: flex;
  flex-direction: column;
  margin: 5px;
  gap: 5px;
}

.total {
  margin-top: 20px;
}

.gear-button {
  border-style: none;
  color: var(--color-text);
  font-size: 25px;
  transition: transform 0.4s;
  /* Animation */
  background-color: var(--color-background-mute);
}

.gear-button:hover {
  transform: scale(1.2);
}

dialog {
  border-radius: calc(5px * var(--ratio));
  box-shadow: 0 0 #0000, 0 0 #0000, 0 25px 50px -12px rgba(0, 0, 0, 0.25);
  padding: 1.6rem;
  width: 30%;
  min-width: 250px;
  max-width: 400px;
  scale: 0;
  transition: all 0.5s;
}

dialog[open] {
  margin: auto;
  opacity: 1;
  scale: 1;
  transition: all 0.5s;
}

/* Included in the Chrome user agent styles */
dialog::backdrop {
  position: fixed;
  top: 0px;
  right: 0px;
  bottom: 0px;
  left: 0px;
  background: rgba(0, 0, 0, 0.7);
  transition: all 0.5s;
}

#options {
  display: grid;
  gap: 10px;
  background-color: var(--color-background-mute);
  color: var(--color-text);
  font-size: 25px;
  transition: transform 0.4s;
  /* Animation */
  background-color: var(--color-background-mute);
}

#options label {
  font-size: 15px;
}

#settings {
  display: flex;
  justify-content: space-between;
}
</style>
