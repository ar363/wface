<script setup lang="ts">
import * as FaceAPI from 'face-api.js'

const modelsLoaded = ref(false)
const streamLoaded = ref(false)
const fdt = ref<NodeJS.Timeout | null>(null)

interface Face {
  x: number,
  y: number,
  w: number,
  h: number,
}

const recfaces = ref<Face[]>([])
const videoObj = useTemplateRef("videoObj")

async function loadModels() {
  await FaceAPI.loadTinyFaceDetectorModel('/models')
  await FaceAPI.loadFaceLandmarkModel('/models')
  await FaceAPI.loadFaceRecognitionModel('/models')

  modelsLoaded.value = true
}

async function loadCamera() {
  const ms = await navigator.mediaDevices.getUserMedia({
    video: true, audio: false,
  })

  // @ts-ignore
  ms.onactive = () => {
    streamLoaded.value = true
  }

  if (videoObj.value)
    videoObj.value.srcObject = ms
}

const opts = new FaceAPI.TinyFaceDetectorOptions({
  inputSize: 512,
  scoreThreshold: 0.5,
})

async function faceDetection() {
  if (!videoObj.value) return

  const faces = await FaceAPI.detectAllFaces(videoObj.value, opts)

  recfaces.value = faces.map((face) => {
    return {
      x: 100 * face.box.x / face.imageWidth,
      y: 100 * face.box.y / face.imageHeight,
      w: 100 * face.box.width / face.imageWidth,
      h: 100 * face.box.height / face.imageHeight,
    }
  })

  fdt.value = setTimeout(() => {
    faceDetection()
  }, 50)
}

loadModels()
loadCamera()

onBeforeUnmount(() => {
  if (fdt.value)
    clearTimeout(fdt.value)
})


</script>

<template>
  <div class="p-4 mx-auto max-w-screen-xl">
    <h1 class="text-2xl font-bold">Rec</h1>

    <div class="mt-4 p-6 rounded-md bg-amber-100 text-amber-800" v-if="!modelsLoaded">Loading models, please wait...
    </div>

    <div class="mt-4 p-6 rounded-md bg-purple-100 text-purple-800" v-if="!modelsLoaded">Setting up video, please wait...
    </div>

    <div class="relative inline-block">
      <video ref="videoObj" autoplay playsinline @loadedmetadata="faceDetection"></video>
      <div v-for="rf of recfaces"
        :style="{ top: rf.y + '%', left: rf.x + '%', width: rf.w + '%', height: rf.h + '%', position: 'absolute', border: '2px solid white' }"
        class="flex items-end justify-end rounded-t-md rounded-bl-md">
        <button class="bg-white -mb-8 -mr-[2px] px-4 py-1 rounded-b-md">Add face</button>
      </div>
    </div>
  </div>
</template>
