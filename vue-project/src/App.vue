<template>
  <div class="p-6 max-w-md mx-auto bg-white rounded-xl shadow-md space-y-4">
    <div class="flex items-center space-x-2">
      <input
        v-model="newJob"
        ref="jobInput"
        class="flex-1 border rounded-lg px-4 py-2 text-gray-700 focus:outline-none"
        type="text"
        placeholder="Enter your job"
      />
      <button
        @click="addJob"
        class="bg-blue-500 text-white px-4 py-2 rounded-lg hover:bg-blue-600"
      >
        Add Job
      </button>
    </div>

    <ul class="space-y-3">
      <li
        v-for="(job, index) in jobs"
        :key="index"
        class="flex items-center justify-between bg-gray-50 px-4 py-2 rounded-lg"
      >
        <div class="flex items-center space-x-2">
          <input
            type="checkbox"
            v-model="job.completed"
            class="h-4 w-4 text-blue-600"
            @change="saveJobsToLocalStorage"
          />
          <span
            :class="{ 'line-through text-gray-400': job.completed }"
            class="text-lg text-gray-800"
          >
            {{ job.name }}
          </span>
        </div>
        <button
          @click="confirmDelete(index)"
          class="bg-red-500 text-white px-3 py-1 rounded-lg hover:bg-red-600"
        >
          Delete
        </button>
      </li>
    </ul>

    <div class="text-gray-700 text-sm">
      Completed tasks: {{ completedJobs }} / {{ jobs.length }}
    </div>

    <div v-if="showModal" class="fixed inset-0 flex items-center justify-center bg-gray-800 bg-opacity-50">
      <div class="bg-white p-6 rounded-lg">
        <p>Are you sure you want to delete this job?</p>
        <button @click="deleteJob" class="bg-red-500 text-white px-3 py-1 rounded-lg mr-2">Yes</button>
        <button @click="showModal = false" class="bg-gray-500 text-white px-3 py-1 rounded-lg">No</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, computed, onMounted } from 'vue'

const newJob = ref('')
const jobs = reactive(JSON.parse(localStorage.getItem('jobs') || '[]'))
const jobToDelete = ref(null)
const showModal = ref(false)

const addJob = () => {
  if (newJob.value.trim()) {
    jobs.push({ name: newJob.value, completed: false })
    newJob.value = ''
    saveJobsToLocalStorage()
  }
}

const confirmDelete = (index) => {
  jobToDelete.value = index
  showModal.value = true
}

const deleteJob = () => {
  if (jobToDelete.value !== null) {
    jobs.splice(jobToDelete.value, 1)
    jobToDelete.value = null
    showModal.value = false
    saveJobsToLocalStorage()
  }
}

const saveJobsToLocalStorage = () => {
  localStorage.setItem('jobs', JSON.stringify(jobs))
}

const completedJobs = computed(() => {
  return jobs.filter(job => job.completed).length
})

onMounted(() => {
  const savedJobs = localStorage.getItem('jobs')
  if (savedJobs) {
    Object.assign(jobs, JSON.parse(savedJobs))
  }
})
</script>

<style>
.completed {
  text-decoration: line-through;
  color: gray;
}
</style>