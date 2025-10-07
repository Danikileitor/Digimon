<script setup>
import { ref } from 'vue'
import DigimonItem from './components/DigimonItem.vue'

const digimonName = ref('')
const digimonNameInput = ref('')
const digimonList = ref([])
import digimonDefaultImage from './assets/digimon.png'
const digimonImage = ref('')
resetDigimon()

// Fetch Digimon desde digi-api cuando el input pierde el foco
async function fetchDigimon() {
  if (!digimonNameInput.value.trim()) {
    digimonList.value = []
    digimonImage.value = ''
    digimonName.value = ''
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
      <input v-model="digimonNameInput" @blur="fetchDigimon" placeholder="Nombre del Digimon" />
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
