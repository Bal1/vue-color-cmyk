<template>
  <div class="vue-color__sketch">
    <div class="vue-color__sketch__saturation-wrap">
      <saturation v-model="colors" @change="childChange"></saturation>
    </div>
    <div class="vue-color__sketch__controls">
      <div class="vue-color__sketch__sliders">
        <div class="vue-color__sketch__hue-wrap-cmyk">
          <hue v-model="colors" @change="childChange"></hue>  
        </div>
      </div>
      <div class="vue-color__sketch__color-wrap">
        <div class="vue-color__sketch__active-color" :style="{background: activeColor}"></div>
      </div>
    </div>
    <div class="vue-color__sketch__field">
      <!-- cmyk -->
      <div class="vue-color__sketch__field--single">
        <ed-in label="c" v-model="cmyk.c" @change="inputChange"></ed-in>
      </div>
      <div class="vue-color__sketch__field--single">
        <ed-in label="m" v-model="cmyk.m" @change="inputChange"></ed-in>
      </div>
      <div class="vue-color__sketch__field--single">
        <ed-in label="y" v-model="cmyk.y" @change="inputChange"></ed-in>
      </div>
      <div class="vue-color__sketch__field--single">
        <ed-in label="k" v-model="cmyk.k" @change="inputChange"></ed-in>
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
  name: 'Sketch',
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
    cmyk () {
      var t = this.colors.rgba

      var r = t.r / 255
      var g = t.g / 255
      var b = t.b / 255

      var k = Math.min(1 - r, 1 - g, 1 - b)
      var c = (1 - r - k) / (1 - k)
      var m = (1 - g - k) / (1 - k)
      var y = (1 - b - k) / (1 - k)

      c = Math.round(c * 100)
      m = Math.round(m * 100)
      y = Math.round(y * 100)
      k = Math.round(k * 100)

      return {c: c, m: m, y: y, k: k}
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
    CMYK (cmyk) {
      var c = cmyk.c / 100
      var m = cmyk.m / 100
      var y = cmyk.y / 100
      var k = cmyk.k / 100

      var r = 1 - Math.min(1, c * (1 - k) + k)
      var g = 1 - Math.min(1, m * (1 - k) + k)
      var b = 1 - Math.min(1, y * (1 - k) + k)

      this.colorChange({
        r: r * 255 || this.colors.rgba.r,
        g: g * 255 || this.colors.rgba.g,
        b: b * 255 || this.colors.rgba.b,
        a: 1,
        source: 'rgba'
      })
    },
    inputChange (data) {
      if (!data) {
        return
      }
      var c = this.cmyk
      for (var k in data) {
        var v = parseFloat(data[k])
        if (v > 100) v = 100
        else if (v < 0) v = 0
        c[k] = v
      }
      this.CMYK(c)
    }
  }
}
</script>

<style lang="stylus">
.vue-color__sketch
  position relative
  width 200px
  padding 10px 10px 0
  box-sizing initial
  background #fff
  border-radius 4px
  box-shadow 0 0 0 1px rgba(0,0,0,.15), 0 8px 16px rgba(0,0,0,.15)
.vue-color__sketch__saturation-wrap
  width 100%
  padding-bottom 75%
  position relative
  overflow hidden
.vue-color__sketch__controls
  display flex
.vue-color__sketch__sliders
  padding 4px 0
  flex 1
  .vue-color__c-hue
  .vue-color__c-alpha__gradient
    border-radius 2px
.vue-color__sketch__hue-wrap-cmyk
  position relative
  height 24px
.vue-color__sketch__hue-wrap-cmyk .vue-color__c-hue__picker
  height 22px
.vue-color__sketch__alpha-wrap
  position relative
  height 10px
  margin-top 4px
  overflow hidden
.vue-color__sketch__color-wrap
  width 24px
  height 24px
  position relative
  margin-top 4px
  margin-left 4px
  border-radius 3px
.vue-color__sketch__active-color
  position absolute
  top 0
  left 0
  right 0
  bottom 0
  border-radius 2px
  box-shadow inset 0 0 0 1px rgba(0,0,0,.15), inset 0 0 4px rgba(0,0,0,.25)
  z-index 2
.vue-color__sketch__field
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
.vue-color__sketch__field--single
  flex 1
  padding-left 6px
.vue-color__sketch__field--double
  flex 2
.vue-color__sketch__presets
  margin-right -10px
  margin-left -10px
  padding-left 10px
  padding-top 10px
  border-top 1px solid #eee
.vue-color__sketch__presets-color
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