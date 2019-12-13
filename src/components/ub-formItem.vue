<template>
  <div :class="{ 'error': isShowMes }">
    <div class="item-wrap">
      <label :for="labelFor" v-if="label" :class="{ 'label-required': isRequired }">{{ label }}</label>
      <slot></slot>
    </div>
    <div v-if="isShowMes" class="message">{{ message }}</div>
  </div>
</template>
<script>
import { Component, Vue, Inject, Provide } from 'vue-property-decorator';
import AsyncValidator from 'async-validator';
import Emitter from './emitter';

@Component({
  name: 'UbFormItem',
  mixins: [Emitter],
  props: {
    label: { type: String, default: '' },
    prop: { type: String }
  }
})
class UbFormItem extends Vue {
  // 写成function的propValueFun 为了 响应式inject
  @Provide() propValue = this.propValueFun;
  @Provide() propName = this.prop;
  @Inject() form;

  isRequired = false;
  isShowMes = false;
  message = '';
  labelFor = 'id' + new Date().valueOf();

  get fieldValue() {
    return this.form.model[this.prop];
  }

  propValueFun() {
    return this.fieldValue;
  }
  mounted() {
    if (this.prop) {
      this.dispatch('UbForm', 'form-add', this);
      // 设置初始值
      this.initialValue = this.fieldValue;
      this.setRules();
    }
  }
  // 组件销毁前，将实例从 Form 的缓存中移除
  beforeDestroy() {
    this.dispatch('UbForm', 'form-remove', this);
  }
  onDestroy() {
    this.message = '';
    this.isShowMes = false;
  }
  setRules() {
    let rules = this.getRules();
    if (rules && rules.length) {
      rules.forEach((rule) => {
        if (rule.required !== undefined) this.isRequired = rule.required;
      });
    }
    this.$on('form-blur', this.onFieldBlur);
    this.$on('form-change', this.onFieldChange);
    this.$on('formItem-destroy', this.onDestroy);
  }
  getRules() {
    let formRules = this.form.rules;
    formRules = formRules ? formRules[this.prop] : [];
    return formRules;
  }
  // 过滤出符合要求的 rule 规则
  getFilteredRule(trigger) {
    const rules = this.getRules();
    if (!rules) return [];
    return rules.filter((rule) => !rule.trigger || rule.trigger.indexOf(trigger) !== -1);
  }
  /**
   * 校验表单数据
   * @param trigger 触发校验类型
   * @param callback 回调函数
   */
  validateItem(trigger, cb) {
    let rules = this.getFilteredRule(trigger);
    if (!rules || rules.length === 0) {
      cb && cb(false);
      return;
    }
    // 使用 async-validator
    const validator = new AsyncValidator({ [this.prop]: rules });
    let model = { [this.prop]: this.form.model[this.prop] };
    validator.validate(model, { firstFields: true }, (errors) => {
      this.isShowMes = errors ? true : false;
      this.message = errors ? errors[0].message : '';
      cb && cb(errors);
    });
  }
  resetField() {
    this.message = '';
    this.form.model[this.prop] = this.initialValue;
  }
  onFieldBlur(val) {
    this.form.model[this.prop] = val;
    this.validateItem('blur');
  }
  onFieldChange(val) {
    this.form.model[this.prop] = val;
    this.validateItem('change');
  }
}
export default UbFormItem;
</script>
<style>
.label-required:before {
  content: '*';
  color: red;
}
.message {
  font-size: 12px;
  color: red;
}
</style>
