<template>
  <!-- 谷歌风格的输入框 -->
  <div class="dh-google-input">
    <input type="text" v-model="inputValue" @change="inputChange" :style="{
                         color: textColor ? textColor : commonColor,
                       }">
    <i :class="['iconfont', 'icon_prefix', prefixButton]" @click="prefixButtonClick"
      :style="{ color: iconColor ? iconColor : commonColor}" v-if="['prefix', 'both'].includes(buttonPosition)"></i>
    <i :class="['iconfont', 'icon_suffix', suffixButton]" @click="suffixButtonClick"
      :style="{ color: iconColor ? iconColor : commonColor}" v-if="['suffix', 'both'].includes(buttonPosition)"></i>

    <span class="slide-border" ref="slide-border" :style="{
              backgroundColor:  borderColor ? borderColor : commonColor,
            }"></span>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        inputValue: ''
      }
    },
    props: {
      value: {
        required: true,
        default: ''
      },
      prefixButton: {
        type: String,
        default: 'icon-guanbi'
      },
      suffixButton: {
        type: String,
        default: 'icon-guanbi'
      },
      buttonPosition: {
        type: String,
        default: 'suffix',
        validator: (val) => {
          return ['suffix', 'prefix', 'both'].includes(val)
        }
      },
      commonColor: {
        type: String,
        default: '#ffffff'
      },
      textColor: {
        type: String,
        default: ''
      },
      borderColor: {
        type: String,
        default: ''
      },
      iconColor: {
        type: String,
        default: ''
      }
    },
    watch: {
      inputValue(val) {
        this.$emit('input', val)
      },
      value: {
        handler(val) {
          this.inputValue = val;
        },
        immediate: true
      },
    },
    methods: {
      suffixButtonClick() {
        this.$emit('suffixButtonClick');
      },
      prefixButtonClick() {
        this.$emit('prefixButtonClick');
      },
      inputChange() {
        this.$emit('change', this.inputValue)
      }
    },
    mounted() {
      this.$nextTick(() => {
        this.$refs['slide-border'].style.transform = 'scaleX(1)'
      })
    },
    beforeDestroy() {
      this.$refs['slide-border'].style.transform = 'scaleX(0)'
    }
  }
</script>

<style lang="scss" scoped>
  .dh-google-input {
    width: 100%;
    height: 100%;
    position: relative;

    input {
      width: 100%;
      height: 100%;
      box-sizing: border-box;

      padding: {
        right: 20px;
      }

      background: #182987;
      color: #ffffff;
    }

    .iconfont {
      position: absolute;
    }

    .icon_prefix {
      left: 5px;
    }

    .icon_suffix {
      // right: 5px;
      right: 0;

      &:hover {
        color: #fff !important;
      }
    }

    .slide-border {
      position: absolute;
      width: 100%;
      height: 1px;
      left: 0;
      bottom: 10px;
      background: #ffffff;
      transform: scaleX(0);
      transition: transform .3s ease-out;
    }
  }
</style>