<template>
  <div class="w-25">
    <input v-model="title" type="text" placeholder="title" class="form-control mt-3 mb-3">
    <div ref="dropzone" class=" btn d-block p-5 bg-dark text-center text-light mt-3 mb-3">
      Uplolad
    </div>
    <input @click.prevent="store" type="submit" value="Add" class="form-control btn-success">

    <div class="mt-5">
      <div v-if="post">
        <h4>{{post.title}}</h4>
        <div v-for="image in post.images">
          <img :src="image.preview_url" class="mb-3">
          <img :src="image.url">
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Dropzone from 'dropzone'

export default {
  name: "Index",

  data() {
    return {
      dropzone: null,
      title: '',
      post: []
    }
  },

  mounted() {
    this.dropzone = new Dropzone(this.$refs.dropzone, {
      url: "/api/posts",
      clickable: true,
      autoProcessQueue: false,
      addRemoveLinks: true
    })

    this.getPost()
  },


  methods: {
    store() {
      const data = new FormData()
      const files = this.dropzone.getAcceptedFiles()
      files.forEach(file => {
        data.append('images[]', file)
        this.dropzone.removeFile(file)
      })
      data.append('title', this.title)
      this.title = ''
      axios.post('/api/posts', data)
          .then(res => {
            this.getPost()
          })
    },

    getPost() {
      axios.get('/api/posts')
          .then(res => {
            this.post = res.data.data
          })
    }

  },
}
</script>

<style scoped>

</style>