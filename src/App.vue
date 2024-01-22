<script setup>
import HelloWorld from './components/HelloWorld.vue'
import { ref, onMounted } from 'vue'

const transcript = ref('')
const isRecording = ref(false)

const Recognition = window.SpeechRecognition || window.webkitSpeechRecognition
const sr = new Recognition()

onMounted(() => {
  sr.continuous = true
  sr.interimResults = true

  sr.onstart = () => {
    console.log('SR started')
    isRecording.value = true
  }
  sr.onend = () => {
    console.log('SR stopped')
    isRecording.value = false
  }

  sr.onresult = event => {
    for (let i = 0; i < event.results.length; i++) {
      const result = event.results[i]
      if (result.isFinal) CheckForCommand(result)
    }

    const t = Array.from(event.results)
      .map(result => result[0])
      .map(result => result.transcript)
      .join('')

    transcript.value = t
  }
})

const CheckForCommand = result => {
  const t = result[0].transcript
  if (t.includes('stop recording')) {
    sr.stop()
  } else if (
    t.includes("what's the time") ||
    t.includes('what is the time') ||
    t.includes('what time is it') ||
    t.includes('what time it is')
  ) {
    sr.stop()
    alert(new Date().toLocaleDateString())
    setTimeout(() => sr.start(), 100)
  }
}

const ToggleMic = () => {
  if (isRecording.value) {
    sr.stop()
  } else {
    sr.start()
  }
}
</script>

<template>
  <div class="app">
    <h2>Hit Record and Say Something</h2>
    <h4>Say "Stop Recording" or Ask "What is the time"</h4>
    <!-- <a href="https://vitejs.dev" target="_blank"> -->
    <img src="/vite.svg" class="logo" alt="Vite logo" />
    <!-- </a> -->
    <!-- <a href="https://vuejs.org/" target="_blank"> -->
    <img src="./assets/vue.svg" class="logo vue" alt="Vue logo" />
    <!-- </a> -->
    <div class="transcript" v-text="transcript"></div>
    <button :class="`mic`" @click="ToggleMic">Record</button>
  </div>
  <!-- <HelloWorld msg="Vite + Vue Web Speech Recognition App" /> -->
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
