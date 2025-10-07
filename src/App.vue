<script setup>
import { ref } from 'vue'
import DigimonItem from './components/DigimonItem.vue'
import digimonDefaultImage from './assets/digimon.png'

const digimonName = ref('')
const digimonNameInput = ref('')
const digimonList = ref([])
const digimonImage = ref('')
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
  }
}

function resetDigimon() {
  digimonList.value = []
  digimonImage.value = digimonDefaultImage
  digimonName.value = 'Digimon'
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
    <div v-if="digimonList.length">
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

.digimon-input {
  padding: 0.5em 1em;
  font-size: 1.1em;
  border: 2px solid #4b9cd3;
  border-radius: 8px;
  outline: none;
  transition: border-color 0.2s, box-shadow 0.2s;
  margin-top: 1em;
  margin-bottom: 1em;
  background: #f7fafd;
  color: #222;
  box-shadow: 0 2px 8px rgba(75, 156, 211, 0.07);
}

.digimon-input:focus {
  border-color: #2d6fa3;
  box-shadow: 0 0 0 2px #b3e0ff;
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
