<template>
  <div class="p-6 max-w-xl mx-auto">
   
    <h1 class="text-2xl font-bold mb-4"> Gestion des tâches</h1>
    <p>Total de tâches : <strong>{{ totalTasks }}</strong></p>

   
    <div class="flex gap-2 mb-4">
      <button
        v-for="filter in filters"
        :key="filter.value"
        @click="currentFilter = filter.value"
        :class="[
          'px-3 py-1 rounded',
          currentFilter === filter.value ? 'bg-blue-500 text-white' : 'bg-gray-200'
        ]"
      >
        {{ filter.label }}
      </button>
    </div>

    <div
      v-for="task in filteredTasks"
      :key="task.id"
      class="border p-3 rounded mb-3 bg-white shadow"
    >
      <h3
        class="font-semibold text-lg"
        :class="{ 'line-through text-gray-500': task.completed }"
      >
        {{ task.title }}
      </h3>
      <p class="text-gray-600 mb-2">{{ task.description }}</p>

      
      <button
        v-show="!task.completed"
        @click="markAsCompleted(task.id)"
        class="bg-green-500 text-white px-2 py-1 rounded hover:bg-green-600"
      >
        Marquer comme terminer
      </button>
      <span v-if="task.completed" class="text-green-600 font-semibold">Terminer</span>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const tasks = ref([])
const currentFilter = ref('all')
const filters = [
  { label: 'Toutes', value: 'all' },
  { label: 'À faire', value: 'todo' },
  { label: 'Terminées', value: 'done' }
]

// Chargement des donnes depuis le fichier JSON
onMounted(async () => {
  const response = await fetch('./taskList.json')
  tasks.value = await response.json()
})

//  Filtrage avec computed
const filteredTasks = computed(() => {
  if (currentFilter.value === 'todo') return tasks.value.filter(t => !t.completed)
  if (currentFilter.value === 'done') return tasks.value.filter(t => t.completed)
  return tasks.value
})


//  terminée
const markAsCompleted = (id) => {
  const task = tasks.value.find(t => t.id === id)
  if (task) task.completed = true
}


const totalTasks = computed(() => tasks.value.length)
</script>

<style scoped>
.line-through {
  text-decoration: line-through;
}
</style>





