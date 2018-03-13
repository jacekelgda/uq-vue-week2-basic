<template>
  <div>
    <div class="row">
      <container 
        name="A" 
        :channelHeight="channelHeight"
      ></container>
      <channel 
        :channelHeight="channelHeight"
        inContainer="A"
        outContainer="B"
      ></channel>
      <container 
        name="B" 
        :channelHeight="channelHeight"
        :containerOffset="containerOffset"
      ></container>
    </div>
    <div class="row">
      <input type="text" placeholder="Channel level" v-model.number="channelHeight" />
      <input type="text" placeholder="Container offset" v-model.number="containerOffset" />
      <button @click="addLiquid">Start</button>
    </div>
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
        channelHeight: 20,
        containerOffset: 50
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