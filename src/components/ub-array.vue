<template>
  <div class="in-wrap" style="min-height: 76px">
    <div class="t-row" v-for="(aItem, index) in currentValue.multiSelectArr" :key="index">
      <input
        v-if="type !== 'radio'"
        type="checkBox"
        :name="name"
        :value="index"
        @change="changeBoxFun($event, index)"
      />
      <input
        v-if="type === 'radio'"
        :type="type"
        :name="name"
        :value="index"
        v-model="currentValue.multiSelectArrValue"
      />
      <input type="text" placeholder="key" v-model="aItem.keyText" @change="changeFun" />
      <input type="text" placeholder="value" v-model="aItem.valueText" @change="changeFun" />
      <button type="button" class="add" v-if="index == 0" @click="addMutiArr(index)"></button>
      <button type="button" class="del" v-if="index != 0" @click="delMutiArr(index)"></button>
    </div>
  </div>
</template>
<script>
import { Component, Vue, Inject } from 'vue-property-decorator';
import Emitter from './emitter';

@Component({
  name: 'UbArray',
  mixins: [Emitter],
  props: {
    value: { type: String, default: '' },
    type: { type: String, default: '' }
  }
})
class UbArray extends Vue {
  @Inject() propValue;

  currentValue = {
    multiSelectArr: [
      {
        keyText: '',
        valueText: ''
      }
    ],
    multiSelectArrValue: []
  };
  name = this.$parent.labelFor;

  created() {
    if (this.value.multiSelectArr) {
      this.currentValue = this.value;
    }
    if (this.propValue() && this.propValue().multiSelectArr) {
      this.currentValue = this.propValue();
    }
  }

  changeBoxFun(e, value) {
    console.log(e.target.checked, value);
    const checked = e.target.checked;
    if (this.type !== 'radio') {
      if (checked && !this.currentValue.multiSelectArrValue.includes(value)) this.currentValue.multiSelectArrValue.push(value);
      if (!checked) this.currentValue.multiSelectArrValue = this.currentValue.multiSelectArrValue.filter((ele) => ele !== value);
    }

    this.dispatch('UbFormItem', 'form-change', this.currentValue);
    this.$emit('change', this.currentValue);
  }

  changeFun() {
    this.dispatch('UbFormItem', 'form-change', this.currentValue);
    this.$emit('change', this.currentValue);
  }
  addMutiArr() {
    this.currentValue.multiSelectArr.push({
      keyText: '',
      valueText: ''
    });
  }

  delMutiArr(index) {
    this.currentValue.multiSelectArr.splice(index, 1);
    this.currentValue.multiSelectArrValue = this.currentValue.multiSelectArrValue.filter((ele) => ele !== index);
  }
}
export default UbArray;
</script>
