<template>
  <div class="in-wrap">
    <textarea
      ref="domRef"
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
  name: 'UbTextarea',
  mixins: [Emitter],
  props: {
    type: { type: String, default: 'text' },
    update: {},
    placeholder: { type: String, default: '' }
  }
})
class UbTextarea extends Vue {
  @Inject() propValue;

  currentValue;
  created() {
    this.currentValue = this.propValue();
  }

  @Watch('update')
  onChangeValue(newState) {
    console.log('textarea-=update', newState, this.propValue());

    this.currentValue = this.propValue();
    this.dispatch('UbFormItem', 'form-change', this.currentValue);
    this.$emit('change', this.currentValue);
    this.$forceUpdate();
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
export default UbTextarea;
</script>
