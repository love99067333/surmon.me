<template>
  <div id="wallpaper" :class="{ dark: isDarkTheme }">
    <div class="switcher" @click="toggleWallpaper">
      <div class="up">
        <div class="title">
          <i18n>
            <template #zh>
              <span class="zh-text">山河入梦</span>
            </template>
            <template #en>
              <i class="iconfont icon-bing" />
              <span class="en-text">BING</span>
            </template>
          </i18n>
        </div>
      </div>
      <div class="down"></div>
    </div>
  </div>
  <client-only>
    <popup :visible="isOnWallpaper" @close="toggleWallpaper">
      <wallpapers @close="toggleWallpaper" />
    </popup>
  </client-only>
</template>

<script lang="ts">
  import { defineComponent, computed } from 'vue'
  import { useEnhancer } from '/@/app/enhancer'
  import { useWallpaperStore } from '/@/store/wallpaper'
  import { GAEventCategories } from '/@/constants/gtag'
  import { Language } from '/@/language/data'
  import Wallpapers from './wall.vue'

  export default defineComponent({
    name: 'Wallpaper',
    components: {
      Wallpapers
    },
    setup() {
      const { i18n, gtag, globalState, isDarkTheme } = useEnhancer()
      const wallpaperStore = useWallpaperStore()
      const isOnWallpaper = computed(() => globalState.switchBox.wallpaper)
      const wallpapers = computed(() => wallpaperStore.papers(i18n.language.value as Language))

      const toggleWallpaper = () => {
        gtag?.event('wallpaper', {
          event_category: GAEventCategories.Widget
        })

        if (wallpapers.value?.length) {
          globalState.switchTogglers.wallpaper()
        } else {
          alert('Something went wrong！')
        }
      }

      return {
        language: i18n.language,
        isDarkTheme,
        isOnWallpaper,
        toggleWallpaper
      }
    }
  })
</script>

<style lang="scss" scoped>
  @import 'src/styles/init.scss';

  #wallpaper {
    $offset: 6px;
    position: fixed;
    left: 0;
    top: 70%;
    cursor: pointer;

    &.dark {
      .switcher {
        .up {
          .title {
            color: $text-reversal;
          }
        }
      }
    }

    .switcher {
      width: 4rem;
      height: 8rem;
      opacity: 0.6;
      display: block;
      position: relative;
      transform: translateX(-$offset * 2);
      transition: opacity $transition-time-fast, transform $transition-time-fast;

      &:hover {
        opacity: 0.8;
        transform: translateX(-$offset);
      }

      @keyframes wallpaper-y {
        0% {
          transform: translateY(-$offset);
        }
        50% {
          transform: translateY($offset);
        }
        100% {
          transform: translateY(-$offset);
        }
      }

      > .up,
      > .down {
        width: 3rem;
        height: 7rem;
        position: absolute;
        border-top-right-radius: $xs-radius;
        border-bottom-right-radius: $xs-radius;
      }

      .down {
        top: 0;
        left: $offset;
        z-index: $z-index-normal + 1;
        background-color: $primary;
        animation: wallpaper-y 1.5s 0.75s infinite;
      }

      .up {
        top: 0;
        left: 0;
        z-index: $z-index-normal + 2;
        background-color: $yellow;
        animation: wallpaper-y 1.5s 0s infinite;

        .title {
          display: flex;
          justify-content: center;
          width: 100%;
          height: 100%;
          line-height: 2rem;
          font-family: 'webfont-bolder', DINRegular;
          writing-mode: tb-rl;
          color: $primary;

          .zh-text {
            margin-bottom: $xs-gap;
            letter-spacing: -3px;
          }

          .en-text {
            font-size: $font-size-small - 1;
            margin-top: $xs-gap;
            letter-spacing: 2px;
            text-align: center;
            font-weight: bold;
          }
        }
      }
    }
  }
</style>
