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
    <div v-if="Object.keys(formData).length > 0">
      <pre>{{ formData }}</pre>
    </div>

    <!-- Submit button to log form data -->
    <button @click="submitForm">Submit</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import ImageUpload from './components/FileInput.vue'

const formData = ref({
  activity_license_image: null,
  last_changes_newspaper_image: null,
  founding_announcement_image: null,
  director_national_card_image: null,
  director_passport_image: null,
  commercial_card_image: null,
  activity_license_image_company: null
})

// Method to update form data when image is uploaded or cleared
const updateFormData = (imageData, key) => {
  formData.value[key] = imageData
}

// Method to handle form submission
const submitForm = () => {
  console.log('Form Data:', formData.value)
}
</script>

<style>
/* Your CSS styles remain unchanged */
.container{
  display:flex;
  justify-content:center;
  gap:0.5rem;
  margin:auto auto;
}
</style>
