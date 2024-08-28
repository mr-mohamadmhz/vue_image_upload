<template>
  <div >
    <div class="container">
        <ImageUpload
          v-for="(value, key) in formData"
          :key="key"
          :modelValue="value"
          :id="key"
          @update:modelValue="updateFormData($event, key)"
        />
    </div>

    <!-- Display form data as JSON -->
    <div class="container col" v-if="Object.keys(formData).length > 0">
      <pre>{{ formData }}</pre>

      <!-- Submit button to log form data -->
      <button @click="submitForm">Submit</button>
    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue'
import ImageUpload from './components/FileInput.vue'

const formData = ref({
  image_0: null,
  image_1: null,
  image_2: null,
  image_3: null,
  image_4: null,
  image_5: null,
  image_6: null
})

// Method to update form data when image is uploaded or cleared
const updateFormData = (imageData, key) => {
  formData.value[key] = imageData
}

// Method to convert image file to base64
const toBase64 = (file) => {
  return new Promise((resolve, reject) => {
    const reader = new FileReader()
    reader.onloadend = () => resolve(reader.result)
    reader.onerror = reject
    reader.readAsDataURL(file)
  })
}

// Method to handle form submission
const submitForm = async () => {
  const base64Data = {}

  for (const key in formData.value) {
    const image = formData.value[key]
    if (image && image.file) {
      try {
        base64Data[key] = await toBase64(image.file)
      } catch (error) {
        console.error(`Error converting ${key} to base64:`, error)
      }
    } else {
      base64Data[key] = null
    }
  }

  console.log('Form Data with Base64:', base64Data)
}
</script>

<style>
.container{
  display:flex;
  justify-content:center;
  gap:0.5rem;
  margin:auto auto;
}
.col{
  flex-direction: column;
  align-items: center;
}
</style>
