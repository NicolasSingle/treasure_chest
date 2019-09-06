<template functional>
  <transition
    name="commonFade"
    :mode="props.mode"
    @after-leave="props.onTransitionAfterLeave"
  >
    <slot></slot>
  </transition>
</template>
<style lang='scss'>
.commonFade-enter-active,
.commonFade-leave-active {
  transition: opacity .15s ease-in-out;
}
.commonFade-enter,
.commonFade-leave-to {
  opacity: 0;
}
</style>
<script>
export default {
  props: {
    mode: {
      type: String,
      validator: value => {
        return ["out-in", "in-out"].includes(value);
      }
    },
    onTransitionAfterLeave: {
      type: Function,
      default: () => {}
    }
  }
};
</script>