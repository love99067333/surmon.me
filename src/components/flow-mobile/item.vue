<template>
  <div class="article-item" @click="handleClick">
    <div class="thumbnail">
      <span
        class="oirigin"
        :class="{
          original: isOriginal,
          reprint: isReprint,
          hybrid: isHybrid
        }"
      >
        <i18n :lkey="LANGUAGE_KEYS.ORIGIN_ORIGINAL" v-if="isOriginal" />
        <i18n :lkey="LANGUAGE_KEYS.ORIGIN_REPRINT" v-else-if="isReprint" />
        <i18n :lkey="LANGUAGE_KEYS.ORIGIN_HYBRID" v-else-if="isHybrid" />
      </span>
      <div
        class="image"
        :style="{ backgroundImage: `url(${getThumbnailURL(article.thumb)})` }"
        :alt="article.title"
        :title="article.title"
      />
    </div>
    <div class="content">
      <div class="body">
        <h4 class="title">
          <router-link class="link" :to="getArticleDetailRoute(article.id)" :title="article.title">
            {{ article.title }}
          </router-link>
        </h4>
        <p
          class="description"
          style="-webkit-box-orient: vertical"
          v-html="article.description"
        ></p>
      </div>
      <div class="meta">
        <span class="date">
          <i class="iconfont icon-clock"></i>
          <span>{{ humanlizeDate(article.create_at) }}</span>
        </span>
        <span class="views">
          <i class="iconfont icon-eye"></i>
          <span>{{ article.meta.views || 0 }}</span>
        </span>
        <span class="comments">
          <i class="iconfont icon-comment"></i>
          <span>{{ article.meta.comments || 0 }}</span>
        </span>
        <span class="likes">
          <i class="iconfont icon-heart" :class="{ liked: isLiked }"></i>
          <span>{{ article.meta.likes || 0 }}</span>
        </span>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
  import { defineComponent, computed, PropType } from 'vue'
  import { useEnhancer } from '/@/app/enhancer'
  import { Article } from '/@/store/article'
  import { useUniversalStore } from '/@/store/universal'
  import { LANGUAGE_KEYS } from '/@/language/key'
  import { getArticleDetailRoute } from '/@/transforms/route'
  import { getMobileArticleListThumbnailURL } from '/@/transforms/thumbnail'
  import { isOriginalType, isHybridType, isReprintType } from '/@/transforms/state'
  import { timeAgo } from '/@/transforms/moment'

  export default defineComponent({
    name: 'FlowArticleListItem',
    props: {
      article: {
        type: Object as PropType<Article>,
        required: true
      }
    },
    setup(props) {
      const { i18n, router, globalState } = useEnhancer()
      const universalStore = useUniversalStore()
      const isLiked = computed(() => universalStore.isLikedPage(props.article.id))
      const isHybrid = isHybridType(props.article.origin)
      const isReprint = isReprintType(props.article.origin)
      const isOriginal = isOriginalType(props.article.origin)

      const humanlizeDate = (date: string) => {
        return timeAgo(date, i18n.language.value as any)
      }

      const getThumbnailURL = (thumbURL: string) => {
        return getMobileArticleListThumbnailURL(thumbURL, globalState.imageExt.value.isWebP)
      }

      const handleClick = () => {
        router.push(getArticleDetailRoute(props.article.id))
      }

      return {
        LANGUAGE_KEYS,
        isLiked,
        isHybrid,
        isReprint,
        isOriginal,
        getThumbnailURL,
        getArticleDetailRoute,
        handleClick,
        humanlizeDate
      }
    }
  })
</script>

<style lang="scss" scoped>
  @import 'src/styles/init.scss';

  .article-item {
    display: flex;
    flex-direction: column;
    position: relative;
    background-color: $module-bg;
    @include radius-box($sm-radius);

    .thumbnail {
      width: 100%;
      height: auto;
      overflow: hidden;
      position: relative;
      background-color: $module-bg-darker-3;

      .oirigin {
        $opacity: 0.7;
        position: absolute;
        right: 0;
        top: 0;
        z-index: $z-index-normal + 1;
        padding: 0 $lg-gap;
        border-bottom-left-radius: $xs-radius;
        opacity: 0.7;
        line-height: 2.3rem;
        font-size: $font-size-small;
        font-weight: bold;
        color: $white;
        text-align: center;
        text-transform: uppercase;
        @include visibility-transition();

        &.original {
          background-color: rgba($surmon, $opacity);
        }
        &.hybrid {
          background-color: rgba($accent, $opacity);
        }
        &.reprint {
          background-color: rgba($red, $opacity);
        }
      }

      .image {
        min-height: calc((100vw - 2rem) * 0.34);
        max-height: 12rem;
        background-size: cover;
        background-position: center;
      }
    }

    .content {
      display: block;

      .body {
        padding: $gap;

        .title {
          margin-top: 0;
          margin-bottom: $sm-gap;
          font-weight: bold;
          @include text-overflow();

          .link {
            text-decoration: none;
          }
        }

        .description {
          margin: 0;
          line-height: 2em;
          overflow: hidden;
          color: $text-secondary;
          text-overflow: ellipsis;
          @include clamp(3);
        }
      }

      .meta {
        display: flex;
        justify-content: space-between;
        align-items: baseline;
        padding: $gap;
        border-top: 1px dashed $module-bg-darker-3;
        overflow: hidden;
        color: $text-disabled;
        font-size: $font-size-small;
        text-overflow: ellipsis;
        white-space: nowrap;
        word-wrap: normal;

        .iconfont {
          margin-right: $sm-gap;
        }

        .views {
          min-width: 4rem;
        }

        .likes {
          .liked {
            color: $red;
          }
        }

        .likes,
        .comments {
          min-width: 3em;
        }
      }
    }
  }
</style>
