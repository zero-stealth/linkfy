<template>
  <div>
    <form @submit.prevent="handleSubmit" enctype="multipart/form-data" class="form-container">
      <div class="form-wrapper">
        <h1>Add Group link</h1>
        <div class="form-group">
          <label for="title">Group title:</label>
          <input
            v-model="title"
            type="text"
            class="form-g-input"
            placeholder="penguin"
            id="title"
          />
        </div>
        <div class="form-group">
          <label for="logo">Group Logo:</label>
          <input
            @change="handleFileUpload"
            type="file"
            class="form-g-input"
            id="Image"
            accept="image/*"
          />
        </div>
        <div class="form-group">
          <label for="link">Link:</label>
          <input
            v-model="link"
            type="text"
            class="form-g-input"
            placeholder="http://example.com"
            id="link"
          />
        </div>
        <div class="form-group">
          <label for="description">Description:</label>
          <input
            v-model="description"
            type="text"
            class="form-g-input"
            placeholder="description for the group"
            id="description"
          />
        </div>
        <div class="form-group">
          <label for="country">Country</label>
          <select v-model="country" class="form-g-input" id="country">
            <option disabled value="">Choose country</option>
            <option value="united States">United States</option>
            <option value="united Kingdom">United Kingdom</option>
            <option value="canada">Canada</option>
            <option value="australia">Australia</option>
          </select>
        </div>
        <button type="submit" class="btn-f-f">Post link</button>
      </div>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'
import { useToast } from 'vue-toastification'

const link = ref('')
const title = ref('')
const logo = ref(null)
const country = ref('')
const toast = useToast()
const description = ref('')

const SERVER_HOST = import.meta.env.VITE_SERVER_HOST

function handleFileUpload(event) {
  const file = event.target.files[0]
  if (file) {
    logo.value = file
  }
}

async function handleSubmit() {
  if (
    title.value.trim() !== '' &&
    logo.value !== null &&
    link.value.trim() !== '' &&
    country.value.trim() !== '' &&
    description.value.trim() !== ''
  ) {
    try {
      const formData = new FormData()
      formData.append('title', title.value)
      formData.append('logo', logo.value)
      formData.append('link', link.value)
      formData.append('country', country.value)
      formData.append('description', description.value)

      const user = JSON.parse(localStorage.getItem('token'))
      await axios.post(`${SERVER_HOST}/data/links/`, formData, {
        headers: {
          'Content-Type': 'multipart/form-data',
          Authorization: `Bearer ${user}`
        }
      })
      toast.success('link added successfully!')
    } catch (err) {
      toast.error('An error occurred while adding the group link.')
    }
  } else {
    toast.error('No empty fields allowed')
  }
}
</script>

<style>
@import '@/style/form.css';
</style>
