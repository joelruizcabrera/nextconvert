<template>
  <div class="download__container">
    <div class="download__headline">
      <h1>NextConvert</h1>
      <p>Paste the video URL below and select your format.</p>
    </div>
    <hr>
    <div class="download__formular">
      <FeDownloader v-bind:formats="formats"></FeDownloader>
    </div>
  </div>
</template>

<script>
import FeDownloader from '@/components/FeDownloader'

export default {
  name: 'Home',
  data () {
    return {
      formats: [
        { value: 'mp3', name: 'MP3' },
        { value: 'mp4', name: 'MP4' },
        { value: 'mov', name: 'MOV' },
        { value: 'mpeg', name: 'MPEG' },
        { value: 'wav', name: 'WAV' },
        { value: 'webm', name: 'WebM' },
        { value: 'webp', name: 'WebP' },
        { value: 'avi', name: 'AVI' }
      ]
    }
  },
  components: {
    FeDownloader
  }
}
</script>

<style lang="scss" scoped>
.download {
  &__container {
    background: #e5e5e5;
    padding: 1rem;
  }
  &__headline h1,
  &__headline p {
    margin-bottom: 0;
    margin-top: 0;
  }
}
</style>
