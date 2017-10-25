<template>
  <span>
    <a v-bind:href="blobUrl" class="card-link" target="_blank">Download: {{ fileName }}</a>
    <br>
  </span>
</template>

<script>
  export default {
    props: ['blobUrl', 'fileName']
  }
</script>

<style>
</style>
