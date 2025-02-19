<template>
  <li class="flower-item" :style="styles" :class="{ playing: state.playing }">
    {{ options.text }}
  </li>
</template>

<script lang="ts">
  import { defineComponent, computed, reactive, nextTick, onMounted, onBeforeUnmount } from 'vue'

  enum Event {
    Ended = 'ended'
  }

  export default defineComponent({
    name: 'WallFlowerItem',
    props: {
      options: {
        type: Object,
        required: true
      },
      zIndex: {
        type: Number,
        required: true
      }
    },
    emits: [Event.Ended],
    setup(props, context) {
      const state = reactive({
        // 播放时长
        delay: 1,
        // 定时器
        timer: null as number | null,
        // 播放状态
        playing: false
      })

      const styles = computed(() => ({
        top: props.options.y + 'px',
        left: props.options.x + 'px',
        'z-index': 99999 + props.zIndex
      }))

      const startAnimation = () => {
        nextTick(() => {
          // Start animation
          setTimeout(() => {
            state.playing = true
          })

          // End animation
          state.timer = window.setTimeout(() => {
            state.playing = false
            nextTick(() => {
              context.emit(Event.Ended, props.options.id)
            })
          }, state.delay * 1000)
        })
      }

      startAnimation()
      onMounted(startAnimation)
      onBeforeUnmount(() => {
        if (state.timer) {
          clearTimeout(state.timer)
          state.timer = null
        }
      })

      return { state, styles }
    }
  })
</script>

<style lang="scss" scoped>
  @import 'src/styles/init.scss';

  .flower-item {
    opacity: 1;
    position: fixed;
    display: block;
    transform: translate3d(-50%, -50%, 0);
    transition: opacity 0.5s;
    color: $primary;
    font-family: 'webfont-bolder', $font-family-normal;
    font-size: $font-size-h3;

    @keyframes flower-playing {
      0% {
        transform: translate3d(-50%, -50%, 0);
      }
      80% {
        transform: translate3d(-50%, -400%, 0);
        opacity: 0.8;
      }
      100% {
        transform: translate3d(-50%, -500%, 0);
        opacity: 0;
      }
    }

    &.playing {
      animation: flower-playing 1s ease 0s forwards;
    }
  }
</style>
