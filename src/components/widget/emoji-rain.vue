<template>
  <div id="emoji-rain" :class="{ active: state.kichikuing }">
    <canvas v-if="state.chambering" ref="rainBase" class="rain-base"></canvas>
  </div>
</template>

<script lang="ts">
  import { defineComponent, ref, reactive, nextTick, onMounted } from 'vue'
  import { getTargetCDNURL } from '/@/transforms/url'

  declare global {
    interface Window {
      $luanchEmojiRain(options: any): void
    }
  }

  export default defineComponent({
    name: 'EmojiRain',
    setup() {
      const rainBase = ref<HTMLElement>(null as any)
      const state = reactive({
        chambering: false,
        kichikuing: false
      })

      onMounted(() => {
        window.$luanchEmojiRain = (options) => {
          if (!state.chambering && !state.kichikuing) {
            state.chambering = true
            nextTick(() => {
              // @ts-ignore
              rainBase.value.width =
                document.documentElement.clientWidth || document.body.clientWidth
              // @ts-ignore
              rainBase.value.height =
                document.documentElement.clientHeight || document.body.clientHeight
              new window.$Emoji233333({
                base: rainBase.value,
                scale: 0.7,
                speed: 12,
                increaseSpeed: 0.4,
                density: 5,
                staggered: true,
                emoji: getTargetCDNURL('/images/emojis/funny.png'),
                ...options,
                onStart() {
                  state.kichikuing = true
                },
                onEnded() {
                  state.kichikuing = false
                  state.chambering = false
                }
              }).launch()
            })
          }
        }
      })

      return {
        state,
        rainBase
      }
    }
  })
</script>

<style lang="scss" scoped>
  @import 'src/styles/init.scss';

  #emoji-rain {
    position: fixed;
    top: 0;
    left: 0;
    z-index: $z-index-underground;

    &.active {
      z-index: $z-index-top;
    }

    .rain-base {
      width: 100%;
      height: 100%;
    }
  }
</style>
