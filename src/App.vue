<template>
  <div id="app">
    <h1>Vue clips</h1>
    <div>
      <button ref="record">Record</button>
      <button ref="stop">Stop</button>
    </div>
    <section ref="sound-clips">
      <div v-bind:key="clip.name" v-for="clip in clips">
        <article>
          <audio controls>
            <source :src="clip.url" type="audio/ogg" />
          </audio>
          <p>{{clip.name}}</p>
        </article>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {},
  data() {
    return {
      clips: [],
    }
  },
  created() {
    if (navigator.mediaDevices) {
      const constraints = {
        audio: true,
      }
      navigator.mediaDevices
        .getUserMedia(constraints)
        // success callback
        .then((stream) => {
          const mediaRecorder = new MediaRecorder(stream)
          const record = this.$refs.record
          const stop = this.$refs.stop

          record.onclick = () => {
            mediaRecorder.start()
            console.log(mediaRecorder.state)
            console.log('recorder started')
            record.style.background = 'red'
            record.style.color = 'black'
          }

          let chunks = []
          console.log(chunks)
          mediaRecorder.ondataavailable = (e) => {
            chunks.push(e.data)
          }

          stop.onclick = () => {
            mediaRecorder.stop()
            console.log(mediaRecorder.state)
            console.log('recorder stopped')
            record.style.background = ''
            record.style.color = ''
          }

          mediaRecorder.onstop = () => {
            console.log('recorder stopped')

            let clipName = prompt('Enter a name for your sound clip')

            const blob = new Blob(chunks, { type: 'audio/ogg; codecs=opus' })
            chunks = []

            const audioURL = window.URL.createObjectURL(blob)

            this.clips = [...this.clips, { name: clipName, url: audioURL }]
          }
        })

        // error callback
        .catch((err) => console.log(err))
    } else {
      console.log('getUserMedia is not supported in your browser')
    }
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
