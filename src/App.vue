<template>
  <div class="outer">
    <div>complain.gripe</div>
    <div>I want to...</div>
    <div class="options">
      <button
        @click="tab = 'complain'"
        class="nav-option"
        :class="tab === 'complain' ? 'nav-option-selected' : ''"
      >
        Complain
      </button>
      <button
        @click="tab = 'browse'"
        class="nav-option"
        :class="tab === 'browse' ? 'nav-option-selected' : ''"
      >
        Browse
      </button>
    </div>

    <form v-if="tab === 'complain'" @submit.prevent="handleSubmit" class="inner">
      <p>Type a complaint into this box and someone will read it</p>
      <textarea v-model="complaint"></textarea>
      <button type="submit">Submit</button>
    </form>

    <div v-if="tab === 'browse'" class="complaints-list">
      <div>Complaints from other people:</div>
      <div v-for="c of complaints" :key="`complaint-${c.id}`">{{ c.content }}</div>
    </div>
  </div>
</template>

<style scoped>
.outer {
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 100vh;
  gap: 10px;
}

.options {
  display: flex;
  gap: 20px;
}

.nav-option {
  padding: 10px 20px;
  border: 1px solid black;
  border-radius: 5px;
  cursor: pointer;
  background-color: white;
}

.nav-option-selected {
  background-color: lightgray;
}

.inner {
  display: flex;
  flex-direction: column;
  height: 100%;
  gap: 10px;
}

.complaints-list {
  display: flex;
  flex-direction: column;
  height: 100%;
  gap: 20px;
}

p {
  margin: 0;
}

textarea {
  height: 200px;
  padding: 5px;
}

button {
  width: 100%;
}
</style>

<script setup lang="ts">
import { onMounted, ref } from 'vue'

const tab = ref('complain')
const complaint = ref('')
const complaints = ref<{ id: string; content: string; created_at: string }[]>([])

async function handleSubmit() {
  await postComplaint()
  complaint.value = ''
  fetchComplaints()
}

async function fetchComplaints() {
  const response = await fetch('https://hash.spudlocker.com/random/complaints')
  const data = (await response.json()) as { id: string; content: string; created_at: string }[]

  complaints.value = data
}

async function postComplaint() {
  await fetch('https://hash.spudlocker.com/random/complaint', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ complaint: complaint.value }),
  })
}

onMounted(() => {
  fetchComplaints()
})
</script>
