<template>
  <form @submit="onSubmit">
    <slot></slot>
  </form>
</template>
<script>
import { Component, Vue, Provide } from 'vue-property-decorator';
@Component({
  name: 'UbForm',
  props: {
    model: { type: Object },
    rules: { type: Object }
  }
})
class UbForm extends Vue {
  fields = [];
  @Provide() form = this;

  resetFields() {
    this.fields.forEach((field) => field.resetField());
  }
  validate(cb) {
    return new Promise((resolve) => {
      let valid = true,
        count = 0;
      this.fields.forEach((field) => {
        field.validateItem('', (error) => {
          if (error) valid = false;
          if (++count === this.fields.length) {
            resolve(valid);
            if (typeof cb === 'function') cb(valid, field.form.model, { [field.prop]: error});
          }
        });
      });
    });
  }
  onSubmit(e) {
    const { $listeners } = this;
    if (!$listeners.submit) {
      e.preventDefault();
    } else {
      this.$emit('submit', e);
    }
  }
  created() {
    this.$on('form-add', (field) => {
      if (field) this.fields.push(field);
    });
    this.$on('form-remove', (field) => {
      if (field.prop) this.fields.splice(this.fields.indexOf(field), 1);
    });
  }
}
export default UbForm;
</script>
