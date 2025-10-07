<template>
  <div class="digimon-card">
    <h2 class="digimon-title">{{ name }}</h2>
    <div class="digimon-content">
      <button class="side-btn left-btn" @click="$emit('left-click')">◀</button>
      <img :src="image" :alt="name" class="digimon-image" />
      <button class="side-btn right-btn" @click="$emit('right-click')">▶</button>
    </div>
  </div>
</template>

<script setup>
defineProps({
  name: String,
  image: String
});
</script>

<style scoped>
.digimon-card {
  width: 320px;
  border: 1px solid #ccc;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.07);
  padding: 16px;
  background: #fff;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.digimon-title {
  margin-bottom: 12px;
  text-align: center;
  font-size: 1.4rem;
  font-weight: bold;
  color: black;
}

.digimon-content {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
}

.digimon-image {
  width: 120px;
  height: 120px;
  object-fit: contain;
  margin: 0 16px;
}

.side-btn {
  background: #f5f5f5;
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  font-size: 1.5rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background 0.2s;
}

.side-btn:hover {
  background: #e0e0e0;
}
</style>
