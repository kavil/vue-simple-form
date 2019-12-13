<template>
  <div class="in-wrap">
    <select ref="domRef" placeholder="请选择" @change="handleChange" :value="currentValue">
      <slot></slot>
    </select>
  </div>
</template>
<script>
import { Component, Vue, Inject, Watch } from 'vue-property-decorator';
import Emitter from './emitter';

@Component({
  name: 'UbSselect',
  mixins: [Emitter],
  props: {
    value: { type: String, default: '' },
    update: {}
  }
})
class UbSselect extends Vue {
  @Inject() propValue;

  currentValue;
  created() {
    console.log(this.propValue());
    this.currentValue = this.propValue() || this.value;
  }

  // 这个手动update方法 是需求所致 平常可以去除
  // 需求里当参数类型改变 可能 组件类型不变 所以要手动更新
  @Watch('update')
  onChangeValue(newState) {
    console.log(newState, '<----select-=update');
    
    this.currentValue = this.propValue();
    this.dispatch('UbFormItem', 'form-change', this.currentValue);
    this.$emit('change', this.currentValue);
    this.$forceUpdate();
  }

  mounted() {
    if (this.$parent.labelFor) this.$refs.domRef.id = this.$parent.labelFor;
  }
  handleChange(e) {
    const value = e.target.value;
    this.currentValue = value;
    this.dispatch('UbFormItem', 'form-change', value);
    this.$emit('change', value);
  }
  handleBlur() {
    this.dispatch('UbFormItem', 'form-blur', this.currentValue);
  }
}
export default UbSselect;
</script>
