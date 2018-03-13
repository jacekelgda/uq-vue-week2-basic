<template>
    <div
        class="channel"
        :style="channelStyle"
    >
    <div
      class="liquid"
      :style="liquidStyle"
    />
  </div>
</template>

<script>
    import { eventBus } from '../main'
    export default {
        props: ['channelHeight', 'inContainer', 'outContainer'],
        data: function() {
            return {
                empty: true,
                inContainerReachedLevel: false,
                outContainerReachedLevel: false
            }
        },
        computed: {
            channelStyle() {
                return `top: ${180 - this.channelHeight}px;`
            },
            liquidStyle() {
                let height = this.empty ? 0 : 100
                return `height: ${height}%`
            }
        },
        methods: {
            onLiquidReachedChannelLevel(data) {
                if (data.container == this.inContainer) {
                    this.inContainerReachedLevel = true
                    this.empty = false
                } else if (data.container == this.outContainer) {
                    this.outContainerReachedLevel = true
                }
            },
            transferLiquid() {
                eventBus.$emit('liquidWasAdded', {container: this.outContainer})
            },
            /** Possible Refactor needed below */
            onLiquidAdded() {
                if (this.inContainerReachedLevel && !this.outContainerReachedLevel) {
                    let data = {}
                    data[this.inContainer] = { value: -10}
                    data[this.outContainer] = { value: 10}
                    eventBus.$emit('channelPressureWasChanged', data)
                } else if (this.inContainerReachedLevel && this.outContainerReachedLevel) {
                    let data = {}
                    data[this.inContainer] = { value: -5}
                    data[this.outContainer] = { value: 5}
                    eventBus.$emit('channelPressureWasChanged', data)
                } else {
                    let data = {}
                    data[this.inContainer] = { value: 0}
                    data[this.outContainer] = { value: 0}
                    eventBus.$emit('channelPressureWasChanged', data)
                }
            }
        },
        created() {
            eventBus.$on('liquidReachedChannelLevel', data => this.onLiquidReachedChannelLevel(data))
            eventBus.$on('liquidWasAdded', data => this.onLiquidAdded(data))
        }
    }
</script>

<style scoped>
    .channel {
        background: rgb(93, 182, 134);
        height: 5px;
        width: 50px;
        border-bottom: 15px solid rgb(56, 70, 93);
        border-top: 15px solid rgb(56, 70, 93);
        margin-left: 135px;
        z-index: 1;
        position: absolute;
        top: 180px;
    }
    .liquid {
        width: 100%;
        background-color: rgb(133, 235, 232);
        transition: height 500ms;
        color: white;
        text-align: center;
        font-size: 0.75rem;
        align-self: flex-end;
    }
</style>