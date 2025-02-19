<template>
  <div class="index-page">
    <carrousel
      class="carrousel"
      :articles="articleListStore.list.data"
      :fetching="articleListStore.list.fetching"
    />
    <twitter
      class="twitter"
      :userinfo="twitterStore.userinfo.data || void 0"
      :tweets="twitterStore.tweets.data || void 0"
      :fetching="
        twitterStore.userinfo.fetching ||
        twitterStore.tweets.fetching ||
        articleListStore.list.fetching
      "
    />
    <article-list
      :mammon="false"
      :fetching="articleListStore.list.fetching"
      :articles="articleListStore.list.data"
      :pagination="articleListStore.list.pagination"
      @loadmore="loadmoreArticles"
    />
  </div>
</template>

<script lang="ts">
  import { defineComponent } from 'vue'
  import { useEnhancer } from '/@/app/enhancer'
  import { useUniversalFetch, onClient } from '/@/universal'
  import { useTwitterStore } from '/@/store/twitter'
  import { useArticleListStore } from '/@/store/article'
  import { useMetaStore } from '/@/store/meta'
  import { nextScreen } from '/@/utils/effects'
  import { META } from '/@/config/app.config'
  import ArticleList from '/@/components/flow-desktop/list.vue'
  import Carrousel from './carrousel.vue'
  import Twitter from './twitter.vue'

  export default defineComponent({
    name: 'IndexPage',
    components: {
      Carrousel,
      Twitter,
      ArticleList
    },
    setup() {
      const { meta } = useEnhancer()
      const metaStore = useMetaStore()
      const twitterStore = useTwitterStore()
      const articleListStore = useArticleListStore()

      meta(() => ({
        title: `${META.title} - ${META.sub_title}`,
        description: metaStore.appOptions.data?.description,
        keywords: metaStore.appOptions.data?.keywords.join(',')
      }))

      const loadmoreArticles = async () => {
        const targetPage = articleListStore.list.pagination?.current_page + 1
        await articleListStore.fetchList({ page: targetPage })
        if (targetPage > 1) {
          onClient(nextScreen)
        }
      }

      useUniversalFetch(() => {
        return Promise.all([
          articleListStore.fetchList(),
          twitterStore.fetchUserinfo().catch((e) => {}),
          twitterStore.fetchTweets().catch((e) => {})
        ])
      })

      return {
        twitterStore,
        articleListStore,
        loadmoreArticles
      }
    }
  })
</script>

<style lang="scss" scoped>
  @import 'src/styles/init.scss';

  .index-page {
    .carrousel,
    .twitter {
      margin-bottom: $lg-gap;
    }
  }
</style>
