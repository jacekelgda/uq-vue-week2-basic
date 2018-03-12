<template>
  <div class="row">
    <container name="A" :channelHeight="channelHeight"></container>
    <channel 
    :channelHeight="channelHeight"
    inContainer="A"
    outContainer="B"
    ></channel>
    <container name="B" :channelHeight="channelHeight"></container>
  </div>
</template>

<script>
  import { eventBus } from './main'
  import Container from './components/Container.vue'
  import Channel from './components/Channel.vue'

  export default {
    data: function () {
      return {
        keepAddingLiquid: true,
        channelHeight: 20
      }
    },
    methods: {
      addLiquid() {
        setTimeout(() => {
          if (this.keepAddingLiquid) {
            this.addLiquid()
            eventBus.$emit('liquidWasAdded', {container: 'A'})
          }
        }, 1000)
      }
    },
    watch: {
      keepAddingLiquid() {
        alert('Flow stopped')
      }
    },
    components: {
      container: Container,
      channel: Channel
    },
    created() {
      eventBus.$on('liquidOverflow', () => this.keepAddingLiquid = false)
      this.addLiquid()
    }
  }
</script>

<style>
  body {
    margin: 0px;
  }
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    width: 600px;
  }
  .row {
    display: flex;
  }
</style>