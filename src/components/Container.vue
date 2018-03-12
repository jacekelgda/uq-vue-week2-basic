<template>
    <div
        class="container"
        :style="containerStyle"
    >
    <div
        class="liquid"
        :style="liquidStyle"
    ></div>
    </div>
</template>

<script>
    import { eventBus } from '../main'

    export default {
        props: ['name', 'channelHeight'],
        data: function () {
            return {
                height: 200,
                width: 100,
                liquidHeight: 0,
            }   
        },
        computed: {
            containerStyle() {
                return `
                    height: ${this.height}px;
                    width: ${this.width}px;
                `
            },
            liquidStyle() {
                return `height: ${this.liquidHeight}px`
            }
        },
        watch: {
            liquidHeight() {
                if (this.liquidHeight > this.height) {
                    eventBus.$emit('liquidOverflow', {container: this.name})
                } else if (this.liquidHeight > this.channelHeight) {
                    eventBus.$emit('liquidReachedChannelLevel', {container: this.name})
                }
            }
        },
        methods: {
            onLiquidAdded(data) {
                if (data.container == this.name) {
                    this.liquidHeight += 10
                }
            }
        },
        created() {
            eventBus.$on('liquidWasAdded', data => this.onLiquidAdded(data))
            eventBus.$on('channelPressureWasChanged', (data) => {
                this.liquidHeight += data[this.name].value
            })
        }
    }
</script>

<style scoped>
    .container {
        background: rgb(93, 182, 134);
        height: 200px;
        width: 100px;
        border-left: 15px solid rgb(56, 70, 93);
        border-right: 15px solid rgb(56, 70, 93);
        border-bottom: 15px solid rgb(56, 70, 93);
        display: flex;
        flex-direction: column-reverse;
        margin-left: 20px;
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
