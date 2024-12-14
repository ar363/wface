<script setup>
function addJSONFile() {
  const file = document.getElementById("faces").files[0]
  const reader = new FileReader()

  reader.onload = function (e) {
    const data = JSON.parse(e.target.result)

    let exfaces = localStorage.getItem("faces")
    exfaces = JSON.parse(exfaces || "[]")

    exfaces = [...exfaces, ...data]

    localStorage.setItem("faces", JSON.stringify(exfaces))

    const router = useRouter()
    router.push("/")
  }

  reader.readAsText(file)
}
</script>

<template>
  <div class="max-w-screen md mx-auto p-4">
    <h1 class="text-2xl-font-bold">
      Add faces list (json)
    </h1>

    <input class="file-input file-input-bordered" type="file" name="faces" id="faces" @input="addJSONFile">
  </div>
</template>