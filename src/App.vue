<template>
  <div id="wrapper">
    <app-header></app-header>
    <div class="container">
      <app-torrents></app-torrents>
    </div>
    <app-footer></app-footer>
  </div>
</template>

<script>
import AppHeader from './components/shared/Header.vue';
import AppFooter from './components/shared/Footer.vue';
import AppTorrents from './components/torrents/Torrents.vue';

export default {
  components: {
    'app-header': AppHeader,
    'app-footer': AppFooter,
    'app-torrents': AppTorrents
  }
}
</script>

<style>
  #wrapper > .container {
    padding: 60px 15px 0;
  }
</style>
