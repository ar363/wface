<script setup>
const faces = ref(JSON.parse(localStorage.getItem('faces') || "[]"))

const removeFace = (name) => {
  const newFaces = faces.value.filter(face => face.name !== name)
  localStorage.setItem('faces', JSON.stringify(newFaces))
  faces.value = newFaces
}
</script>

<template>
  <div class="mx-auto p-4 max-w-screen-sm">
    <h1 class="text-2xl font-bold">Storage</h1>
    <ul class="list-disc">
      <li v-for="face in faces" :key="face.name">{{ face.name }} [ <button class="text-red-500" @click="removeFace(face.name)">Remove</button> ]</li>
    </ul>
  </div>
</template>