<template>
  <section class="container flex flex--column flex--justify-content-center flex--align-center">
    <h1 class="text-white">Escolha o arquivo que deseja compactar</h1>
    <div class="container-input" @click="triggerFileInput">
      <h2 :class="nameInput">{{ newNameFile }}</h2>
      <input ref="fileInput" class="hidden" type="file" @change="handleFiles" />
    </div>
    <div class="flex container-clear-buttons">
      <button v-if="flagDownload" @click="downloadZip">Download Zip</button>
      <button class="btn-clear" v-if="flagDownload" @click="clearInput">X</button>
    </div>
  </section>
</template>

<script>
import JSZip from 'jszip'
import { saveAs } from 'file-saver'

export default {
  data() {
    return {
      files: [],
      flagDownload: false,
      input: 'input',
      nameInput: 'container-input--name',
      newNameFile: 'Insira o arquivo para compactar!'
    }
  },
  methods: {
    triggerFileInput() {
      this.$refs.fileInput.click()
    },

    clearInput() {
      this.files = []
      this.flagDownload = false
      this.$refs.fileInput.value = ''
      this.newNameFile = 'Insira o arquivo para compactar!'
    },

    handleFiles(event) {
      this.files = event.target.files
      this.newNameFile = event.target.files[0].name
      this.flagDownload = this.files.length > 0
    },
    async downloadZip() {
      if (this.files.length === 0) {
        alert('Selecione um arquivo!')
        this.flagDownload = false
        return
      } else {
        this.flagDownload = true
      }

      const zip = new JSZip()
      for (let file of this.files) {
        zip.file(file.name, file)
      }

      const content = await zip.generateAsync({ type: 'blob' })
      saveAs(content, `${this.newNameFile}.zip`)

      this.files = []
      this.flagDownload = false
      this.newNameFile = 'Insira o arquivo para compactar!'
    }
  }
}
</script>

<style scoped>
.container {
  max-width: 1366px;
  width: 100%;
  gap: 20px;
}

.container h1 {
  text-align: center;
  font-size: 3rem;
}

.container h2 {
  text-align: center;
}

.container-clear-buttons button {
  cursor: pointer;
  display: inline-block;
  padding: 0.75rem 1.25rem;
  border: none;
  border-radius: 10rem;
  background-color: rgba(0, 183, 255, 0.877);
  color: #fff;
  font-size: 1rem;
  letter-spacing: 0.15rem;
  transition: 0.3s ease-in-out;
  position: relative;
}

.container button:hover {
  background-color: rgba(1, 111, 155, 0.877);
}

.container-clear-buttons {
  gap: 1rem;
}

.container-clear-buttons .btn-clear {
  background-color: rgb(197, 0, 0);
  transition: 0.3s ease-in-out;
}

.container-clear-buttons .btn-clear:hover {
  background-color: rgb(90, 0, 0);
}

.flex {
  display: flex;
}

.flex--column {
  flex-direction: column;
}

.flex--justify-content-center {
  justify-content: center;
}

.flex--align-center {
  align-items: center;
}

.text-white {
  color: white;
}

.hidden {
  overflow: hidden;
  display: none;
}

.container-input {
  cursor: pointer;
  border: 2px solid white;
  border-radius: 1rem;
  padding: 2rem;
  transition: 0.3s ease-in-out;
}

.container-input:hover {
  border: 1px solid white;
  background-color: white;
  color: black;
  border-radius: 1rem;
  padding: 2rem;
}
</style>
