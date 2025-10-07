<script setup>
import DigimonItem from './components/DigimonItem.vue'
</script>

<template>
  <header>
    <div class="wrapper">
      <h1>DIGIMON</h1>
    </div>
  </header>

  <main>
    <DigimonItem name="Agumon" image="https://digi-api.com/images/digimon/w/Agumon.png" />
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    justify-content: center;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
