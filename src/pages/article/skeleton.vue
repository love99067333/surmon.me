<template>
  <div class="article-skeleton">
    <div class="module">
      <div class="content-skeleton">
        <skeleton-line class="title" />
        <skeleton-paragraph class="content" :lines="9" line-height="1.3em" />
      </div>
    </div>
    <div class="module">
      <div class="share-skeleton">
        <skeleton-base class="item" v-for="item in socialCount" :key="item" />
      </div>
    </div>
    <div class="module">
      <ul
        class="releted-skeleton"
        :style="{ 'grid-template-columns': `repeat(${reletedCount}, 1fr)` }"
      >
        <skeleton-base v-for="item in reletedCount" :key="item" class="item" />
      </ul>
    </div>
  </div>
</template>

<script lang="ts">
  import { defineComponent } from 'vue'
  export default defineComponent({
    name: 'ArticleDetailSkeleton',
    props: {
      socialCount: {
        type: Number,
        required: true
      },
      reletedCount: {
        type: Number,
        required: true
      }
    }
  })
</script>

<style lang="scss" scoped>
  @import 'src/styles/init.scss';

  .article-skeleton {
    .module {
      margin-bottom: $lg-gap;
      @include radius-box($sm-radius);
      @include common-bg-module();
    }

    .content-skeleton {
      padding: 1rem 2rem;

      .title {
        width: 60% !important;
        height: 26px !important;
        margin: 2rem auto !important;
      }

      .content {
        margin-top: 3rem;
        margin-bottom: 1rem;
      }
    }

    .share-skeleton {
      padding: $gap;
      display: flex;
      justify-content: space-between;

      .item {
        height: 3rem !important;
        margin-right: $gap;
        &:last-child {
          margin-right: 0;
        }
      }
    }

    .releted-skeleton {
      padding: $gap;
      margin: 0;
      display: grid;
      grid-gap: $gap;
      @include radius-box($sm-radius);
      @include common-bg-module();

      .item {
        width: auto !important;
        height: 8rem !important;
      }
    }
  }
</style>
