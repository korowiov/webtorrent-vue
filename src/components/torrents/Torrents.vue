<template>
  <div class ="row">
    <div class="col-lg-12">
      <div class="input-group">
        <input type="text" class="form-control" placeholder="Magnet link or torrent info hash" aria-label="Magnet link or torrent info hash" v-model="magnetUri">
        <span class="input-group-btn">
          <button v-on:click="download" class="btn btn-secondary" type="button">Add torrent!</button>
        </span>
      </div>
    </div>
    <div class="col-lg-12">
      <app-torrent-details v-for="torrentHash in torrentHashes" v-bind:magnetUri="buildMagnetUri(torrentHash)"></app-torrent-details>
    </div>
  </div>
</template>

<script>
  import AppTorrentDetails from './TorrentDetails.vue';

  export default {
    data: function () {
      return {
        torrentHashes: [],
        magnetUri: ''
      }
    },
    components: {
      'app-torrent-details': AppTorrentDetails
    },
    computed: {
      extractedInfoHash: function () {
        var reg = /(.+btih:)([0-9A-Za-z]+)(\&.+)/
        if (this.magnetUri.match(/^[A-Za-z0-9]+$/g)) {
          return this.magnetUri.toLowerCase()
        }
        return this.magnetUri.replace(reg, "$2").toLowerCase()
      }
    },
    methods: {
      download: function () {
        if (this.torrentHashes.indexOf(this.extractedInfoHash) === -1) {
          this.torrentHashes.push(this.extractedInfoHash)
        }
      },
      buildMagnetUri: function (hash) {
        return "magnet:?xt=urn:btih:" + hash + "&dn=fit5.jpg&tr=udp%3A%2F%2Fexplodie.org%3A6969&tr=udp%3A%2F%2Ftracker.coppersurfer.tk%3A6969&tr=udp%3A%2F%2Ftracker.empire-js.us%3A1337&tr=udp%3A%2F%2Ftracker.leechers-paradise.org%3A6969&tr=udp%3A%2F%2Ftracker.opentrackr.org%3A1337&tr=wss%3A%2F%2Ftracker.btorrent.xyz&tr=wss%3A%2F%2Ftracker.fastcast.nz&tr=wss%3A%2F%2Ftracker.openwebtorrent.com"
      }
    }
  }
</script>

<style>
</style>
