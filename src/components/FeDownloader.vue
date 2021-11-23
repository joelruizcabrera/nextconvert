<template>
  <div id="download__section">
    <div class="download__provider_container" :class="{'close': startedDownload}" v-if="foundProvider">
      <div class="download__provider_text">
        <img :src="'//logo.clearbit.com/' + this.providerInfos.domain">
        <h2>{{this.providerInfos.domain}}</h2>
      </div>
      <div class="download__provider_youtube" v-if="providerInfos.domain === 'youtube.com'">
        <iframe width="560" height="315" :src="'https://www.youtube.com/embed/' + this.providerInfos.data[1]" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
      </div>
      <div class="download__provider_soundcloud" v-if="providerInfos.domain === 'soundcloud.com'">
        <iframe width="560" height="315" scrolling="no" frameborder="no" allow="autoplay" :src="'https://w.soundcloud.com/player/?url=' + this.providerInfos.data.input + '&color=%23ff5500&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=false&visual=true'"></iframe>
      </div>
      <div class="download__provider_vimeo" v-if="providerInfos.domain === 'vimeo.com'">
        <iframe :src="'https://player.vimeo.com/video/' + this.providerInfos.data[2] + '?h=fe946235c9'" width="560" height="315" frameborder="0" allow="autoplay; fullscreen; picture-in-picture" allowfullscreen></iframe>      </div>
    </div>
    <form>
      <div class="download__input_section">
        <input type="url" v-model="videoUrl" name="download_url" :class="{ 'has-error': urlValidateError }" required>
        <select name="download_format">
          <option v-for="format in this.formats" :key="format.value" :value="format.value">{{format.name}}</option>
        </select>
      </div>
      <div class="download__url_error" v-if="urlValidateError">
        <small>{{this.urlValidateErrorMsg}}</small>
      </div>
      <div class="download__submit_section">
        <button type="button" name="submit" @click="startDownload()">Download</button>
      </div>
    </form>
  </div>
</template>

<script>

export default {
  name: 'FeDownloader',
  props: [
    'formats'
  ],
  data () {
    return {
      videoUrl: '',
      urlValidateError: false,
      urlValidateErrorMsg: '',
      providerRegEx: [
        { provider: 'YouTube', regEx: /^(?:https?:\/\/)?(?:m\.|www\.)?(?:youtu\.be\/|youtube\.com\/(?:embed\/|v\/|watch\?v=|watch\?.+&v=))((\w|-){11})(?:\S+)?$/ },
        { provider: 'Soundcloud', regEx: /^(?:(https?):\/\/)?(?:(?:www|m)\.)?(soundcloud\.com|snd\.sc)\/(.*)$/ },
        { provider: 'Vimeo', regEx: /^(?:(https?):\/\/)?(?:www\.)?vimeo\.com\/(\d+)/ }
      ],
      providerInfos: {
        domain: '',
        data: {}
      },
      foundProvider: false,
      startedDownload: false
    }
  },
  watch: {
    videoUrl (value) {
      const validateResult = this.validateUrl(value)
      if (validateResult) {
        this.videoUrl = value
        this.urlValidateErrorMsg = ''
        this.urlValidateError = false
        const data = this.checkProvider(value)
        if (!data.provider) {
          this.urlValidateErrorMsg = 'Provider was not found'
          this.urlValidateError = true
          this.providerInfos.domain = ''
          this.providerInfos.data = {}
          this.foundProvider = false
        } else {
          this.urlValidateErrorMsg = ''
          this.urlValidateError = false
          const oldDomain = (new URL(value)).hostname
          let domain
          if (oldDomain.split('.').length > 2) {
            domain = oldDomain.split('.')[1] + '.' + oldDomain.split('.')[2]
          } else {
            domain = oldDomain
          }
          this.providerInfos.domain = domain
          this.providerInfos.data = data.fullData
          this.foundProvider = true
        }
      } else {
        this.urlValidateErrorMsg = 'Please insert a valid url'
        this.urlValidateError = true
      }
    }
  },
  methods: {
    validateUrl (url) {
      const expression = /[-a-zA-Z0-9@:%._\\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b([-a-zA-Z0-9()@:%_\\+.~#?&//=]*)?/gi
      const regex = new RegExp(expression)
      if (url !== '') {
        return !!url.match(regex)
      } else {
        return true
      }
    },
    checkProvider (url) {
      let data
      let found = false
      for (let i = 0; i < this.providerRegEx.length; i++) {
        const match = url.match(this.providerRegEx[i].regEx)
        if (match) {
          found = true
          data = {
            provider: this.providerRegEx[i].provider,
            fullData: match
          }
        }
      }
      if (found) {
        console.log(data)
        return data
      } else {
        return false
      }
    },
    startDownload () {
      if (this.foundProvider && !this.urlValidateError) {
        this.startedDownload = true
      }
    }
  }
}
</script>

<style lang="scss" scoped>

.download {
  $inputheight: 2rem;
  &__provider {
    &_container {
      height: 23rem;
      overflow: hidden;
      transition: height .3s ease;
      &.close {
        transition: height .3s ease;
        height: 0;
      }
    }
    &_text {
      display: inline-flex;
      align-items: center;
      img {
        margin-right: 1rem;
        width: auto;
        height: 2rem;
      }
    }
  }
  &__url_error {
    small {
      margin-top: 0;
      color: red;
    }
  }
  &__input_section {
    display: flex;
    justify-content: center;
    flex-direction: row;
    margin-bottom: .25rem;
    input {
      width: 100%;
      height: $inputheight - .222rem;
      padding: 0 0 0 .5rem;
      margin: 0;
    }
    select {
      width: 10rem;
      padding: 0 0 0 .5rem;
      height: $inputheight;
    }
  }
  &__submit_section {
    margin-top: .25rem;
    button {
      width: 100%;
      height: $inputheight;
    }
  }
}
</style>
