<template>
  <div class="search">
    <div class="search-box">
      <input
        id="keyword"
        class="search-input"
        required
        type="search"
        name="search"
        maxlength="16"
        v-model.trim="keyword"
        :class="i18n.language"
        :placeholder="i18n.t(LANGUAGE_KEYS.SEARCH_PLACEHOLDER)"
        @keyup.enter="handleSearch"
      />
      <button class="search-btn" @click="handleSearch">
        <i class="iconfont icon-search" />
      </button>
    </div>
    <router-link class="archive-btn" :to="{ name: RouteName.Archive }">
      <i class="iconfont icon-peachblossom" />
    </router-link>
  </div>
</template>

<script lang="ts">
  import { defineComponent, ref, onMounted } from 'vue'
  import { useEnhancer } from '/@/app/enhancer'
  import { RouteName } from '/@/app/router'
  import { GAEventCategories } from '/@/constants/gtag'
  import { LANGUAGE_KEYS } from '/@/language/key'
  import { isSearchFlow } from '/@/transforms/route'

  export default defineComponent({
    name: 'DesktopAsideSearch',
    setup() {
      const { i18n, gtag, route, router } = useEnhancer()
      const keyword = ref('')

      onMounted(() => {
        if (isSearchFlow(route.name as string)) {
          keyword.value = route.params.keyword as string
        }
      })

      const handleSearch = () => {
        const inputKeyword = keyword.value
        const paramsKeyword = route.params.keyword as string
        const isSearchPage = isSearchFlow(route.name as string)
        if (inputKeyword && (!isSearchPage || paramsKeyword !== inputKeyword)) {
          router.push({
            name: RouteName.SearchFlow,
            params: { keyword: inputKeyword }
          })
          gtag?.event('aside_search', {
            event_category: GAEventCategories.Universal,
            event_label: inputKeyword
          })
        }
      }

      return {
        LANGUAGE_KEYS,
        RouteName,
        i18n,
        keyword,
        handleSearch
      }
    }
  })
</script>

<style lang="scss" scoped>
  @import 'src/styles/init.scss';

  .search {
    padding: $sm-gap;
    width: 100%;
    height: 3em;
    overflow: hidden;
    display: flex;
    justify-content: space-between;

    .search-input,
    .search-btn,
    .archive-btn {
      height: 2em;
      line-height: 2em;
      background-color: $module-bg-darker-1;
      @include background-transition();

      &:hover {
        background-color: $module-bg-hover;
      }
    }

    > .search-box {
      display: flex;
      flex-grow: 1;

      .search-input {
        margin-right: 0;
        flex-grow: 1;
        &::-webkit-calendar-picker-indicator {
          display: none;
        }
      }

      .search-btn {
        width: 2em;
        background-color: $module-bg-darker-3;
        @include background-transition();

        &:hover {
          background-color: $module-bg-darker-4;
        }
      }
    }

    .archive-btn {
      display: inline-block;
      text-align: center;
      margin-left: $sm-gap;
      border-radius: $mini-radius;
      width: 3em;

      .iconfont {
        font-size: $font-size-h4;
      }
    }
  }
</style>
