<template>
  <div role="separator" class="divider" :class="[type, size, { dashed }, { slot: $slots.default }]">
    <slot></slot>
  </div>
</template>

<script lang="ts">
  import { defineComponent, PropType } from 'vue'
  export default defineComponent({
    name: 'Divider',
    props: {
      type: {
        type: String as PropType<'horizontal' | 'vertical'>,
        default: 'horizontal'
      },
      dashed: {
        type: Boolean,
        default: false
      },
      size: {
        type: String as PropType<'default' | 'xs' | 'sm' | 'lg'>,
        default: 'default'
      }
    }
  })
</script>

<style lang="scss" scoped>
  @import 'src/styles/init.scss';

  .divider {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    &.default {
      #{--divider-gap}: $gap;
    }
    &.xs {
      #{--divider-gap}: $xs-gap;
    }
    &.sm {
      #{--divider-gap}: $sm-gap;
    }
    &.lg {
      #{--divider-gap}: $lg-gap;
    }

    &.horizontal {
      display: flex;
      align-items: center;
      clear: both;
      width: 100%;
      margin: var(--divider-gap) 0;
      border-top: 1px solid $text-divider;
      &.slot {
        border-top: none;
      }
    }

    &.vertical {
      position: relative;
      top: -0.06em;
      display: inline-block;
      height: 0.9em;
      margin: 0 var(--divider-gap);
      vertical-align: middle;
      border-left: 1px solid $text-divider;

      &.slot {
        display: inline-flex;
        flex-direction: row;
        flex-wrap: nowrap;
        align-items: center;
        justify-content: center;
        height: 1em;
        vertical-align: baseline;
        border-left: none;
      }
    }

    &.dashed {
      border-style: dashed;
    }
  }
</style>
