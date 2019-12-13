<template>
  <div class="in-wrap">
    <input
      ref="domRef"
      :type="type"
      :placeholder="placeholder"
      :value="currentValue"
      @input="handleInput"
      @blur="handleBlur"
    />
  </div>
</template>
<script>
import { Component, Vue, Inject, Watch } from 'vue-property-decorator';
import Emitter from './emitter';

@Component({
  name: 'UbInput',
  mixins: [Emitter],
  props: {
    type: { type: String, default: 'text' },
    placeholder: { type: String, default: '' },
    update: {}
  }
})
class UbInput extends Vue {
  // 响应式inject
  @Inject() propValue;

  currentValue;
  created() {
    this.currentValue = this.propValue();
  }

  // 这个手动update方法 是需求所致 平常可以去除
  // 需求里当参数类型改变 可能 组件类型不变 所以要手动更新
  @Watch('update')
  onChangeValue(newState) {
    console.log('input-=update', newState, this.propValue());
    this.currentValue = this.propValue();
    setTimeout(() => {
      // 更新 为了更新原有错误信息
      this.dispatch('UbFormItem', 'form-change', this.currentValue);
      this.$emit('change', this.currentValue);
      this.$forceUpdate();
    });
  }

  mounted() {
    if (this.$parent.labelFor) this.$refs.domRef.id = this.$parent.labelFor;
  }

  beforeDestroy() {
    this.dispatch('UbFormItem', 'formItem-destroy');
  }

  handleInput(e) {
    let value = e.target.value;
    if (e.target.type === 'number') {
      value = Number(value);
    }

    this.currentValue = value;
    this.dispatch('UbFormItem', 'form-change', value);
    this.$emit('input', value);
  }
  handleBlur() {
    this.dispatch('UbFormItem', 'form-blur', this.currentValue);
  }
}
export default UbInput;
</script>
