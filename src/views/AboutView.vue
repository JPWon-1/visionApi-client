<script setup>
import FileUpload from '../components/FileUpload.vue'
import MultipleFileUpload from '../components/MultipleFileUpload.vue'
</script>

<template>
  <div class="about">
    <h1>Single Uploader</h1>
    <FileUpload/>

    <h1>Multiple Uploader</h1>
    <MultipleFileUpload/>
  </div>
</template>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>
