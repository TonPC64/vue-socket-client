<template>
  <div id="app">
    <span>WebSocket</span>
    {{connect}}
    <button @click="send">send</button>
  </div>
</template>

<script>

export default {
  ws: null,
  name: 'App',
  data () {
    return {
      connect: ''
    }
  },
  methods: {
    send () {
      this.$options.ws.send('hello')
    },
    register () {
      return new Promise(function (resolve, reject) {
        var server = new WebSocket('ws://localhost:8000/chann')
        server.onopen = function () {
          resolve(server)
        }
        server.onerror = function (err) {
          console.log('failed')
          reject(err)
        }
      })
    },
    wsConnect () {
      console.log('start connect')
      const intervalId = setInterval(async () => {
        try {
          const server = await this.register()
          this.$options.ws = server
          this.setupClient()
          clearInterval(intervalId)
        } catch (error) {
          console.log({error})
        }
      }, 2 * 1000)
    },
    setupClient () {
      if (this.$options.ws !== null) {
        this.$options.ws.onopen = (evt) => {
          console.log('OPEN')
        }
        this.$options.ws.onclose = (evt) => {
          console.log('CLOSE')
          this.$options.ws = null
          this.wsConnect()
        }
        this.$options.ws.onmessage = function (evt) {
          console.log('RESPONSE: ' + evt.data)
        }
        this.$options.ws.onerror = function (evt) {
          if (this.$options.ws.readyState === 1) {
            console.log('ws normal error: ' + evt.type)
          }
        }
      }
    }
  },
  created () {
    this.wsConnect()
  }
}
</script>

<style>
</style>
