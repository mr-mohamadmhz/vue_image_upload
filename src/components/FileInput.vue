<template>
  <div>
    <div class="image-upload-container">
      <div class="upload-box" @click="triggerFileInput">
        <!-- Hidden file input for uploading images -->
        <input
          type="file"
          accept="image/*"
          :id="fileInputId"
          class="hidden-file-input"
          @change="handleImageUpload"
        />
        <!-- Placeholder text when no image is uploaded -->
        <div v-if="!imageData" class="placeholder">
          <label>Please upload a photo</label>
        </div>
        <!-- Image preview and actions if an image is uploaded -->
        <div v-else class="image-container">
          <img :src="imageData.fileBlob" alt="Uploaded image" class="uploaded-image" />
          <div class="image-actions">
            <button @click.stop="clearImage">
              <img src="src/assets/icon/trash.svg" alt="Remove" class="icon"/>
            </button>
            <button @click.stop="openModal">
              <img src="src/assets/icon/expand.svg" alt="expand" class="icon"/>
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal for enlarging the image -->
    <div v-if="isModalVisible" class="modal" @click="closeModal">
      <img :src="modalImageSrc" alt="Enlarged image" class="modal-image" />
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, watch, defineProps, defineEmits } from 'vue'

interface ImageData {
  file: File
  fileName: string
  fileBlob: string
  fileType: string
}

const props = defineProps<{
  modelValue: ImageData | null
  id: string
}>()

const emit = defineEmits<{
  (event: 'update:modelValue', value: ImageData | null): void
}>()

const imageData = ref<ImageData | null>(props.modelValue)
const fileInputId = `fileInput-${props.id}`
const isModalVisible = ref(false)
const modalImageSrc = ref('')

watch(() => props.modelValue, (newVal) => {
  imageData.value = newVal
})

// Method to trigger file input click
const triggerFileInput = () => {
  const input = document.getElementById(fileInputId) as HTMLInputElement
  input.click()
}

// Method to handle image upload and save file information
const handleImageUpload = (event: Event) => {
  const input = event.target as HTMLInputElement
  const file = input.files?.[0]
  if (file) {
    const fileBlob = URL.createObjectURL(file)
    const newImageData: ImageData = {
      file,
      fileName: file.name,
      fileBlob,
      fileType: file.type
    }
    emit('update:modelValue', newImageData)
  }
}

// Method to clear uploaded image
const clearImage = () => {
  emit('update:modelValue', null)
}

// Method to open the modal with the image
const openModal = () => {
  if (imageData.value) {
    modalImageSrc.value = imageData.value.fileBlob
    isModalVisible.value = true
  }
}

// Method to close the modal
const closeModal = () => {
  isModalVisible.value = false
  modalImageSrc.value = ''
}
</script>

<style scoped>
.image-upload-container {
  margin-bottom: 15px;
}

.upload-box {
  border: 1px solid #ccc;
  width: 200px;
  height: 200px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  position: relative;
}

.placeholder {
  color: #888;
  text-align: center;
  padding: 10px;
}

.image-container {
  position: relative;
  height: 100%;
  width: 100%;
}

.uploaded-image {
  width: 100%;
  height: 100%;
  object-fit: cover !important;
}

.image-actions {
  position: absolute;
  top: 0;
  right: 0;
  padding:0 0 0 10px;
  border-radius:0 0 0 20px;
  box-shadow:0 2px 2px #000;
  display: flex;
  gap: 2px;
  background:#fff;
}

.image-actions button {
  background : none;
  border: none;
  padding: 2px;
  cursor: pointer;
  font-size: 12px;
}

.hidden-file-input {
  position: absolute;
  width: 0;
  height: 0;
  opacity: 0;
  pointer-events: none;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.8);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-image {
  max-width: 90%;
  max-height: 90%;
}

.modal-visible .image-upload-container {
  pointer-events: none;
}

.icon{
  width:25px;
  height:25px;
}
</style>
