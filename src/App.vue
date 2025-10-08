<script setup>
import { ref } from 'vue'
import DigimonItem from './components/DigimonItem.vue'
import digimonDefaultImage from './assets/digimon.png'

const digimonName = ref('')
const digimonNameInput = ref('')
const digimonList = ref([])
const digimonImage = ref('')
const priorEvolutions = ref([])
const nextEvolutions = ref([])

resetDigimon()

// Fetch Digimon desde digi-api cuando el input pierde el foco
async function fetchDigimon() {
  if (!digimonNameInput.value.trim()) {
    resetDigimon()
    return
  }
  digimonName.value = digimonNameInput.value
  try {
    const res = await fetch(`https://digi-api.com/api/v1/digimon?name=${encodeURIComponent(digimonNameInput.value)}`)
    const data = await res.json()
    if (data.content && data.content.length) {
      digimonList.value = data.content
      digimonImage.value = data.content[0].image
      priorEvolutions.value = []
      nextEvolutions.value = []
    } else {
      resetDigimon()
    }
  } catch (e) {
    resetDigimon()
    console.error('Error fetching Digimon:', e)
  }
}

// Seleccionar un Digimon de la lista
function selectDigimon(id) {
  digimonList.value = digimonList.value.filter(d => d.id === id)
  if (digimonList.value.length) {
    digimonName.value = digimonList.value[0].name
    digimonImage.value = digimonList.value[0].image
    priorEvolutions.value = []
    nextEvolutions.value = []
  }
}

async function showPriorEvolutions(digimon) {
  if (!digimon.priorEvolutions || !digimon.priorEvolutions.length) {
    priorEvolutions.value = []
    return
  }
  const evolutions = []
  for (const evo of digimon.priorEvolutions) {
    const res = await fetch(`https://digi-api.com/api/v1/digimon/${evo.id}`)
    const data = await res.json()
    if (data && data.id) {
      evolutions.push({
        ...data,
        image: data.images?.[0]?.href || digimonDefaultImage
      })
    }
  }
  priorEvolutions.value = evolutions
}

async function showNextEvolutions(digimon) {
  if (!digimon.nextEvolutions || !digimon.nextEvolutions.length) {
    nextEvolutions.value = []
    return
  }
  const evolutions = []
  for (const evo of digimon.nextEvolutions) {
    const res = await fetch(`https://digi-api.com/api/v1/digimon/${evo.id}`)
    const data = await res.json()
    if (data && data.id) {
      evolutions.push({
        ...data,
        image: data.images?.[0]?.href || digimonDefaultImage
      })
    }
  }
  nextEvolutions.value = evolutions
}

function resetDigimon() {
  digimonList.value = []
  digimonImage.value = digimonDefaultImage
  digimonName.value = 'Digimon'
  priorEvolutions.value = []
  nextEvolutions.value = []
}

// Funciones recursivas para evoluciones
async function handleLeftClick(digimon) {
  // Consulta por ID para obtener priorEvolutions
  const res = await fetch(`https://digi-api.com/api/v1/digimon/${digimon.id}`)
  const data = await res.json()
  await showPriorEvolutions(data)
  nextEvolutions.value = []
}
async function handleRightClick(digimon) {
  // Consulta por ID para obtener nextEvolutions
  const res = await fetch(`https://digi-api.com/api/v1/digimon/${digimon.id}`)
  const data = await res.json()
  await showNextEvolutions(data)
  priorEvolutions.value = []
}
</script>

<template>
  <header>
    <div class="wrapper">
      <h1>DIGIMON</h1>
      <input v-model="digimonNameInput" @blur="fetchDigimon" placeholder="Nombre del Digimon" class="digimon-input" />
    </div>
  </header>

  <main>
    <div v-if="digimonList.length === 1">
      <div class="evolution-row">
        <div class="evolution-col" v-if="priorEvolutions.length">
          <DigimonItem v-for="digimon in priorEvolutions" :key="digimon.id" :name="digimon.name" :image="digimon.image"
            @left-click="() => handleLeftClick(digimon)" @right-click="() => handleRightClick(digimon)" />
        </div>
        <DigimonItem :name="digimonList[0].name" :image="digimonList[0].image"
          @left-click="() => handleLeftClick(digimonList[0])" @right-click="() => handleRightClick(digimonList[0])" />
        <div class="evolution-col" v-if="nextEvolutions.length">
          <DigimonItem v-for="digimon in nextEvolutions" :key="digimon.id" :name="digimon.name" :image="digimon.image"
            @left-click="() => handleLeftClick(digimon)" @right-click="() => handleRightClick(digimon)" />
        </div>
      </div>
    </div>
    <div v-else-if="digimonList.length">
      <DigimonItem v-for="digimon in digimonList" :key="digimon.id" :name="digimon.name" :image="digimon.image"
        @click="selectDigimon(digimon.id)" />
    </div>
    <div v-else>
      <DigimonItem :name="digimonName || 'Digimon'" :image="digimonImage" @error="resetDigimon" />
    </div>
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

h1 {
  font-size: 2.5em;
  font-weight: bold;
  letter-spacing: 0.08em;
  color: #4b9cd3;
  text-shadow: 0 2px 8px rgba(75, 156, 211, 0.15);
  margin: 0.2em 0;
  text-align: center;
  font-family: 'Segoe UI', 'Arial', sans-serif;
}

.digimon-input {
  padding: 0.5em 1em;
  font-size: 1.1em;
  text-align: center;
  border: 2px solid #4b9cd3;
  border-radius: 8px;
  outline: none;
  transition: border-color 0.2s, box-shadow 0.2s;
  margin-bottom: 1em;
  background: #f7fafd;
  color: #222;
  box-shadow: 0 2px 8px rgba(75, 156, 211, 0.07);
}

.digimon-input:focus {
  border-color: #2d6fa3;
  box-shadow: 0 0 0 2px #b3e0ff;
}

.evolution-row {
  display: flex;
  flex-direction: row;
  align-items: flex-start;
  justify-content: center;
  gap: 24px;
}

.evolution-col {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    flex-direction: column;
    place-items: center;
    justify-content: center;
  }

  header .wrapper {
    display: flex;
    flex-direction: column;
    place-items: center;
    flex-wrap: wrap;
  }
}
</style>
