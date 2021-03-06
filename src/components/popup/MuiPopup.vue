<template>
  <!--
    显性的过渡持续时间：
    默认情况下，Vue会等待其在过渡效果的根元素的第一个transitionend或animationend事件
    然而也可以不这样设定：比如，我们可以拥有一个精心编排的一系列过渡效果，其中一些嵌套的内部元素相比于过渡
    效果的根元素有延迟的或更长的过渡效果

    这时我们可以通过duration属性定制一个显性的过渡持续时间
   -->
  <transition name="slide" :duration="250">
    <div class="mui-popup" v-if="visible">
      <div class="mui-popup-mask" @click="onClickMask"></div>
      <div class="mui-popup-content" :class="`position-${position}`">
        <h3 class="mui-popup-title" v-if="title">{{title}}</h3>
        <div class="mui-popup-content-item">
          <slot></slot>
        </div>
        <div class="mui-popup-close" @click="onClose">
          <mui-icon name="close"></mui-icon>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
  export default {
    name: 'MuiPopup',
    model: {
      prop: 'visible',
      event: 'update:visible'
    },
    props: {
      title: { type: String },
      visible: {
        type: Boolean,
        default: false
      },
      position: {
        type: String,
        validator (value) {
          return ['left', 'top', 'right', 'bottom'].includes(value);
        },
        default: 'bottom'
      },
      maskClosable: {
        type: Boolean,
        default: false
      },
      getContainer: { type: String }
    },
    mounted () {
      if (this.getContainer) {
        document.querySelector(this.getContainer).appendChild(this.$el);
      }
    },
    methods: {
      onClose () {
        this.$emit('update:visible', false);
      },
      onClickMask () {
        if (this.maskClosable) {
          this.onClose();
        }
      }
    }
  };
</script>

<style lang="scss" scoped>
  .mui-popup {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 90;
    &-mask {
      position: absolute;
      width: 100%;
      height: 100%;
      background-color: rgba(0, 0, 0, .4);
    }
    &.slide-enter-active,
    &.slide-leave-active {
      /*子元素过渡技巧：https://github.com/vuejs/vue/issues/3477*/
      .mui-popup-content,
      .mui-popup-mask {
        transition: opacity 250ms, transform 250ms, max-height 250ms;
      }
    }
    &.slide-enter,
    &.slide-leave-to {
      .position-bottom {transform: translateY(100%);}
      .position-top {transform: translateY(-100%);}
      .position-left {transform: translateX(-100%);}
      .position-right {transform: translateX(100%);}
      .mui-popup-mask {opacity: 0;}
    }
    &-content {
      display: flex;
      flex-direction: column;
      position: absolute;
      background-color: $white;
      padding: 0 $space-lg;
      color: $dark-text;
      &.position-bottom,
      &.position-top {
        width: 100%;
        height: 516px;
      }
      &.position-bottom {
        bottom: 0;
        border-top-left-radius: $border-radius-md;
        border-top-right-radius: $border-radius-md;
      }
      &.position-top {
        top: 0;
        border-bottom-left-radius: $border-radius-md;
        border-bottom-right-radius: $border-radius-md;
      }
      &.position-left,
      &.position-right {
        top: 0;
        height: 100%;
        width: 80%;
      }
      &.position-left {
        left: 0;
        border-top-right-radius: $border-radius-md;
        border-bottom-right-radius: $border-radius-md;
      }
      &.position-right {
        right: 0;
        border-top-left-radius: $border-radius-md;
        border-bottom-left-radius: $border-radius-md;
      }
    }
    &-title {
      padding: $space-lg $space-xxl;
      text-align: center;
      border-bottom: 1px solid $light-border;
      font-size: $font-lg;
    }
    &-close {
      position: absolute;
      top: $space-lg - $space-sm;
      right: $space-lg - $space-sm;
      font-size: $font-lg;
      padding: $space-sm;
      color: $text-color;
    }
    &-content-item {
      flex: 1;
      overflow: auto;
    }
  }
</style>
