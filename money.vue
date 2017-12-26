<template>
  <div
    class="money el-input"
    :class="{'is-disabled':disabled,'el-input-group el-input-group--append':!!$slots.append}">
    <input
      ref="ipt"
      autocomplete="off"
      type="text"
      rows="2"
      :disabled="disabled"
      validateevent="true"
      class="el-input__inner"
      v-model="val">
    <div
      v-if="!!$slots.append"
      class="el-input-group__append">
      <slot name="append"></slot>
    </div>
  </div>
</template>
<script>
  function numToMoney(v, n) {
    const fixed = this.fixed || 2
    let r
    const ipt = this.$refs['ipt']
    if (v === '' || v === undefined) {
      return v
    } else {
      let [a, b = ''] = (v + '').split(/(?=\.)/);
      let x = a.length % 3
      r = a.substr(0, x)
        + a.substr(x).replace(/(\d{3})/g, ',$1').substr(+!x)
      if (n) r += (+(0 + b)).toFixed(n).substr(1)
      else if (b) r += b.substr(0, fixed + 1)
    }
    if (ipt) {
      let st = -1
      if (document.activeElement === ipt) {
        const val = ipt.value + ''
        if (val === '' || /^\.0*$/g.test(val)) {
          this.$emit('input', '')
          this.$emit('change', '')
          return ''
        }
        const point = val.indexOf('.')
        const l0 = val.length
        const l1 = r.length
        const fix = l1 - l0;
        const s0 = ipt.selectionStart
        const s1 = ipt.selectionEnd
        if (point !== -1 && s0 > point)
          st = s0 - fix + (
            fix > 0 ?
              s0 === s1 ?
                s0 - point === fix && fix === 1 ?
                  0 : 1
                : 2
              : -1
          )
        else st = s0 + fix
      }
      ipt.value = r
      if (st !== -1) ipt.setSelectionRange(st, st)
    }
    return r
  }

  export default {
    props: {
      value: {
        required: false,
        'default': ''
      },
      disabled: {
        type: Boolean,
        required: false,
        'default': false
      },
      fixed: {
        type: Number,
        required: false,
      }
    },
    computed: {
      val: {
        get() {
          return numToMoney.call(this, this.value, this.fixed || 2)
        },
        set(v) {
          const fixed = this.fixed || 2;
          const val = this.value
          const fix1 = v.replace(/[^0-9,.]/g, '')
          const fix2 = v.replace(/,/g, '')
          const fix3 = parseFloat(parseFloat(fix2).toFixed(fixed))
          const nan = isNaN(fix3)
          if (fix1 !== v || nan || val === fix3) {
            numToMoney.call(this, this.value, fixed)
          } else if (!nan) {
            this.$emit('input', fix3)
            this.$emit('change', fix3)
          }
        }
      }
    }
  }
</script>
