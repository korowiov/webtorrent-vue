<template>
  <div class="card">
    <div class="card-body">
      <h4 class="card-title">Torrent: {{ torrent.name }}</h4>
      <h6 class="card-subtitle mb-2 text-muted">Hash: {{ torrent.infoHash}}</h6>
      <div class="progress">
        <div
          class="progress-bar progress-bar-striped"
          v-bind:class="[progress == 100 ? 'bg-success' : 'bg-info progress-bar-animated']"
          role="progressbar"
          v-bind:style="{width: progress +'%'}"
          aria-valuenow="progress"
          aria-valuemin="0"
          aria-valuemax="100">
          {{ progress }} %
        </div>
      </div>
      <p class="card-text">Files:</p>
      <app-download-link v-for="link in links" v-bind:blobUrl="link.blobUrl" v-bind:fileName="link.fileName"></app-download-link>
    </div>
  </div>
</template>

<script>
  import AppDownloadLink from './DownloadLink.vue'

  import WebTorrent from 'webtorrent';
  import StreamSaver from 'streamsaver';
  var client = new WebTorrent();

  export default {
    data: function () {
      return {
        torrent: {},
        links: [],
        progress: 0,
        streamSemaphore: -1
      }
    },
    props: ['magnetUri'],
    components: {
      'app-download-link': AppDownloadLink
    },
    methods: {
      updateProgress: function () {
        this.progress = Math.round((this.torrent.received / this.torrent.length) * 100)
        if (this.progress != 100) {
          setTimeout(this.updateProgress, 500);
        }
      },
      streamFile: function () {
        var file = this.torrent.files[this.streamSemaphore];
        var fileStream = StreamSaver.createWriteStream(file.name, file.size);
        var writer = fileStream.getWriter();
        var readStream = file.createReadStream();
        var vm = this;

        readStream.on('data', function (data) {
          writer.write(data);
        })

        readStream.on('end', function () {
          vm.streamSemaphore = -1
          writer.close();
        })
      }
    },
    watch: {
      streamSemaphore: function(currentValue, oldValue) {
        if(currentValue == -1 && oldValue < (this.torrent.files.length - 1)) {
          this.streamSemaphore = oldValue + 1;
          this.streamFile();
        }
      }
    },
    created() {
      var vm = this
      client.add(this.magnetUri, {announce: ['ws://127.0.0.1:8800/announce'], path: '/Downloads/'}, function (torrent) {
        vm.torrent = torrent;
        vm.streamSemaphore = 0;
        vm.streamFile();
        setTimeout(vm.updateProgress, 500);
      })
    }
  }
</script>

<style scoped>
  .card {
    margin-top: 20px
  }
  .progress {
    margin-top: 20px;
    margin-bottom: 20px;
  }
  p {
    margin-bottom: 0px
  }
</style>
