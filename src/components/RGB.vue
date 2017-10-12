<template>
  <div class="vue-color__RGB">
    <div class="vue-color__RGB__saturation-wrap">
      <saturation v-model="colors" @change="childChange"></saturation>
    </div>
    <div class="vue-color__RGB__controls">
      <div class="vue-color__RGB__sliders">
        <div class="vue-color__RGB__hue-wrap-cmyk">
          <hue v-model="colors" @change="childChange"></hue>  
        </div>
      </div>
      <div class="vue-color__RGB__color-wrap">
        <div class="vue-color__RGB__active-color" :style="{background: activeColor}"></div>
      </div>
    </div>
    <div class="vue-color__RGB__field">
      <!-- cmyk -->
      <div class="vue-color__RGB__field--single">
        <ed-in label="r" v-model="colors.rgba.r" @change="inputChange"></ed-in>
      </div>
      <div class="vue-color__RGB__field--single">
        <ed-in label="g" v-model="colors.rgba.g" @change="inputChange"></ed-in>
      </div>
      <div class="vue-color__RGB__field--single">
        <ed-in label="b" v-model="colors.rgba.b" @change="inputChange"></ed-in>
      </div>
    </div>
  </div>
</template>

<script>
import colorMixin from '../mixin/color'
import editableInput from './common/EditableInput.vue'
import saturation from './common/Saturation.vue'
import hue from './common/Hue.vue'

export default {
  name: 'RGB',
  mixins: [colorMixin],
  components: {
    saturation,
    hue,
    'ed-in': editableInput
  },
  data () {
    return {
      cm: {
        c: 0,
        m: 0,
        y: 0,
        k: 0
      }
    }
  },
  computed: {
    rgba () {
      return this.colors.rgba
    },
    activeColor () {
      var rgba = this.colors.rgba
      return 'rgba(' + [rgba.r, rgba.g, rgba.b, rgba.a].join(',') + ')'
    }
  },
  methods: {
    handlePreset (c) {
      this.colorChange({
        hex: c,
        source: 'hex'
      })
    },
    childChange (data) {
      this.colorChange(data)
    },
    inputChange (data) {
      if (!data) {
        return
      }
      for (var k in data) {
        var v = parseFloat(data[k])
        if (v > 100) v = 100
        else if (v < 0) v = 0
        this.rgba[k] = v
      }
      this.colorChange(this.rgba)
    }
  }
}
</script>

<style lang="stylus">
.vue-color__RGB
  position relative
  width 200px
  padding 10px 10px 0
  box-sizing initial
  background #fff
  border-radius 4px
  box-shadow 0 0 0 1px rgba(0,0,0,.15), 0 8px 16px rgba(0,0,0,.15)
.vue-color__RGB__saturation-wrap
  width 100%
  padding-bottom 75%
  position relative
  overflow hidden
.vue-color__RGB__controls
  display flex
.vue-color__RGB__sliders
  padding 4px 0
  flex 1
  .vue-color__c-hue
  .vue-color__c-alpha__gradient
    border-radius 2px
.vue-color__RGB__hue-wrap-cmyk
  position relative
  height 24px
.vue-color__RGB__hue-wrap-cmyk .vue-color__c-hue__picker
  height 22px
.vue-color__RGB__alpha-wrap
  position relative
  height 10px
  margin-top 4px
  overflow hidden
.vue-color__RGB__color-wrap
  width 24px
  height 24px
  position relative
  margin-top 4px
  margin-left 4px
  border-radius 3px
.vue-color__RGB__active-color
  position absolute
  top 0
  left 0
  right 0
  bottom 0
  border-radius 2px
  box-shadow inset 0 0 0 1px rgba(0,0,0,.15), inset 0 0 4px rgba(0,0,0,.25)
  z-index 2
.vue-color__RGB__field
  display flex
  padding-top 4px
  .vue-color__editable-input__input
    width 80%
    padding 4px 10% 3px
    border none
    box-shadow inset 0 0 0 1px #ccc
    font-size 11px
  .vue-color__editable-input__label
    display block
    text-align center
    font-size 11px
    color #222
    padding-top 3px
    padding-bottom 4px
    text-transform capitalize
.vue-color__RGB__field--single
  flex 1
  padding-left 6px
.vue-color__RGB__field--double
  flex 2
.vue-color__RGB__presets
  margin-right -10px
  margin-left -10px
  padding-left 10px
  padding-top 10px
  border-top 1px solid #eee
.vue-color__RGB__presets-color
  border-radius 3px
  overflow hidden
  position relative
  display inline-block
  margin 0 10px 10px 0
  vertical-align top
  cursor pointer
  width 16px
  height 16px
  box-shadow inset 0 0 0 1px rgba(0,0,0,.15)
</style>