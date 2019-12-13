<template>
  <div class="in-wrap">
    <input
      id="i1"
      type="radio"
      :name="propName"
      :checked="currentValue === true"
      :value="true"
      @change="handleChange(true)"
    />
    <label for="i1" style="margin-right: 40px">是</label>
    <input
      id="i2"
      type="radio"
      :name="propName"
      :checked="currentValue === false"
      :value="false"
      @change="handleChange(false)"
    />
    <label for="i2">否</label>
  </div>
</template>
<script>
import { Component, Vue, Inject, Watch } from 'vue-property-decorator';
import Emitter from './emitter';

@Component({
  name: 'UbRadioBool',
  mixins: [Emitter],
  props: {
    value: { type: Boolean }
  }
})
class UbRadioBool extends Vue {
  @Inject() propValue;
  @Inject() propName;

  currentValue;
  created() {
    if (this.value !== undefined) {
      this.currentValue = this.value;
      return;
    }
    if (this.propValue() !== undefined) this.currentValue = this.propValue();
  }

  @Watch('value')
  onChangeValue(newState) {
    if (newState) {
      this.currentValue = newState;
      this.dispatch('UbFormItem', 'form-change', newState);
      this.$emit('change', newState);
      this.$forceUpdate();
    }
  }

  handleChange(e) {
    const value = e;
    this.currentValue = value;
    this.dispatch('UbFormItem', 'form-change', value);
    this.$emit('change', value);
  }
}
export default UbRadioBool;
</script>
