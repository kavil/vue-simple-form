<template>
  <div class="add-prop-mask">
    <div class="add-prop-page">
      <div class="add-prop-top">
        <img src="./assets/logo-small.png" class="app-logo" />
        <span>属性编辑</span>
      </div>

      <Ub-Form
        ref="form"
        :model="formData"
        :rules="rules"
        @submit="handleSubmit"
        class="add-prop-main"
      >
        <div class="code-table">
          <Ub-FormItem label="参数名称：" prop="parmaEnName" class="row-cell">
            <Ub-Input type="text" placeholder="调用的参数英文名称" />
          </Ub-FormItem>
          <Ub-FormItem label="参数原型：" prop="parmaCnName" class="row-cell">
            <Ub-Input type="text" placeholder="参数中文名称" />
          </Ub-FormItem>
          <Ub-FormItem label="参数说明：" prop="parmaDes" class="row-cell">
            <Ub-Textarea placeholder="参数说明" />
          </Ub-FormItem>

          <Ub-FormItem label="参数类型：" prop="componParmaType" class="row-cell">
            <Ub-Select @change="componParmaTypeChange">
              <option v-for="(op, i) in componParmaTypeList" :key="i" :value="op.value">{{op.label}}</option>
            </Ub-Select>
            <div class="tip-wrap">
              <div class="icon">
                <img src="./assets/extend/extend_order_tip_icon.png" alt />
              </div>
              <div class="tip-cell">
                <p>组件类型中“多选”和“下拉框”选项需输入对应“预置值”，采用 JSON 格式。</p>
              </div>
            </div>
          </Ub-FormItem>
          <Ub-FormItem label="组件类型：" prop="componType" class="row-cell">
            <Ub-Select @change="componTypeChange" :update="formData.componType">
              <option
                v-for="(op, i) in componObj[formData.componParmaType]"
                :key="i"
                :value="op.value"
              >{{op.label}}</option>
            </Ub-Select>

            <div class="tip-wrap">
              <div class="icon">
                <img src="./assets/extend/extend_order_tip_icon.png" alt />
              </div>
              <div class="tip-cell">
                <p>参数值的类型</p>
              </div>
            </div>
          </Ub-FormItem>

          <Ub-FormItem
            v-if="formData.componType === 'text'"
            label="默认值："
            prop="defaultValue"
            class="row-cell"
          >
            <Ub-Input
              :update="[formData.componParmaType, formData.componType]"
              :type="componParmaTypeDefault.type || 'text'"
            />
          </Ub-FormItem>
          <Ub-FormItem
            v-if=" formData.componType === 'textArea'"
            label="默认值："
            prop="defaultValue"
            class="row-cell"
          >
            <Ub-Textarea :update="[formData.componParmaType, formData.componType]" />
          </Ub-FormItem>
          <Ub-FormItem
            v-if="formData.componType === 'select' && formData.componParmaType === 'boolean'"
            label="默认值："
            prop="defaultValue"
            class="row-cell"
          >
            <Ub-Radio-bool class="cell-m" :value="formData.defaultValue" />
          </Ub-FormItem>
          <Ub-FormItem
            v-if=" formData.componType === 'array'"
            label="多选预制值："
            prop="defaultValue"
            class="row-cell"
          >
            <Ub-Array :value="componParmaTypeDefault.defaultV" />
            <div class="tip-wrap">
              <div class="icon">
                <img src="./assets/extend/extend_order_tip_icon.png" alt />
              </div>
              <div class="tip-cell">
                <p>组件类型中“多选”和“下拉框”选项需输入对应“预置值”，采用 JSON 格式。</p>
              </div>
            </div>
          </Ub-FormItem>
          <Ub-FormItem
            v-if=" formData.componType === 'select' && formData.componParmaType === 'array'"
            label="下拉框预制值："
            prop="defaultValue"
            class="row-cell"
          >
            <Ub-Array type="radio" :value="componParmaTypeDefault.defaultV" />
            <div class="tip-wrap">
              <div class="icon">
                <img src="./assets/extend/extend_order_tip_icon.png" alt />
              </div>
              <div class="tip-cell">
                <p>组件类型中“多选”和“下拉框”选项需输入对应“预置值”，采用 JSON 格式。</p>
              </div>
            </div>
          </Ub-FormItem>
        </div>
        <pre>
{{formData || json}}
        </pre>

        <div class="btns-wrap">
          <!-- <button class="back" @click="back">返回(B)</button> -->
          <button class="hold" html-type="submit">保存(S)</button>
        </div>
      </Ub-Form>
    </div>
  </div>
</template>


<script>
import { Component, Vue } from 'vue-property-decorator';
import UbForm from '@/components/ub-form.vue';
import UbFormItem from '@/components/ub-formItem.vue';
import UbInput from '@/components/ub-input.vue';
import UbTextarea from '@/components/ub-textarea.vue';
import UbSelect from '@/components/ub-select.vue';
import UbRadioBool from '@/components/ub-radio-bool.vue';
import UbArray from '@/components/ub-array.vue';
@Component({
  name: 'App',
  components: {
    UbForm,
    UbFormItem,
    UbInput,
    UbTextarea,
    UbSelect,
    UbRadioBool,
    UbArray
  }
})
class App extends Vue {
  delay = true;
  formData = {
    componParmaType: 'variable',
    componType: 'text',
    defaultValue: '""'
  };
  rules = {
    parmaEnName: [{ required: true, message: '参数名称不能为空', trigger: 'change' }],
    parmaCnName: [{ required: true, message: '参数原型不能为空', trigger: 'change' }],
    parmaDes: [{ required: true, message: '参数说明不能为空', trigger: 'change' }],
    componParmaType: [{ message: '请选择', trigger: 'change' }],
    componType: [{ message: '请选择', trigger: 'change' }],
    defaultValue: [{ required: true, message: '不能为空' }]
  };
  componParmaTypeList = [
    { value: 'variable', label: '变量', defaultV: '""' },
    { value: 'boolean', label: '布尔', defaultV: true },
    {
      value: 'dictionary',
      label: '字典',
      defaultV: '[]'
    },
    { value: 'string', label: '字符串', defaultV: '' },
    { value: 'array', label: '数组', defaultV: '[]' },
    { value: 'number', label: '数字', defaultV: 0, type: 'number' }
  ];
  componObj = {
    variable: [{ value: 'text', label: '输入框' }],
    boolean: [{ value: 'select', label: '下拉框' }],
    dictionary: [
      {
        value: 'textArea',
        label: '多行输入框',
        rule: [{ required: true, message: '格式错误', validator: this.validateData, trigger: 'change' }]
      }
    ],
    string: [{ value: 'text', label: '输入框' }, { value: 'textArea', label: '多行输入框' }],
    array: [
      { value: 'text', label: '输入框', rule: [{ required: true, message: '格式错误', validator: this.validateData, trigger: 'change' }] },
      {
        value: 'textArea',
        label: '多行输入框',
        rule: [{ required: true, message: '格式错误', validator: this.validateData, trigger: 'change' }]
      },
      {
        value: 'array',
        label: '多选框'
      },
      {
        value: 'select',
        label: '下拉框'
      }
    ],
    number: [{ value: 'text', label: '输入框' }]
  };

  validateData(rule, value, callback) {
    let err;
    try {
      JSON.parse(value);
    } catch (error) {
      err = '错误';
    }
    // if (value.multiSelectArr) {
    //   for (let i = 0; i < value.multiSelectArr.length; i++) {
    //     const ele = value.multiSelectArr[i];
    //     if (!ele.keyText || !ele.valueText) {
    //       console.log(ele);

    //       err = '错误';
    //       break;
    //     }
    //   }
    // }
    callback(err);
  }

  get componParmaTypeDefault() {
    return this.componParmaTypeList.find((ele) => ele.value === this.formData.componParmaType);
  }

  componParmaTypeChange(e) {
    this.$set(this.formData, 'componType', this.componObj[e][0].value);
    this.componTypeChange(this.componObj[e][0].value);
    setTimeout(() => {
      this.$forceUpdate();
    });
  }

  componTypeChange(e) {
    this.$set(this.formData, 'defaultValue', this.componParmaTypeDefault.defaultV);

    const componParam = this.componObj[this.formData.componParmaType];
    const { rule } = componParam.find((ele) => ele.value === e);

    console.log(e, componParam, rule);
    if (rule) {
      this.rules.defaultValue = rule;
    } else {
      this.rules.defaultValue = [{ required: true, message: '不能为空' }];
    }
    setTimeout(() => {
      this.$forceUpdate();
    });
  }

  handleSubmit(e) {
    e.preventDefault();
    this.$refs.form.validate((valid, params, error) => {
      console.log(valid, params, error);
      if (valid) {
        console.log('校验成功');
      } else {
        console.log('校验失败');
      }
    });
  }
  handleReset() {
    this.$refs.form.resetFields();
  }
}
export default App;
</script>

<style lang="less">
.add-prop-mask {
  // display: none;
  position: absolute;
  left: 0;
  bottom: 0;
  right: 0;
  top: 0;
  z-index: 10;
  background: rgba(0, 0, 0, 0.6);
  box-sizing: border-box;
  .add-prop-page {
    width: 340px;
    height: auto;
    box-sizing: border-box;
    border: 0.5px solid #5177c7;
    background: white;
    transform: translate(-50%, -50%);
    margin-left: 500px;
    margin-top: 325px;
    margin-top: 300px;
    .add-prop-top {
      width: 100%;
      height: 32px;
      background: #5076c8;
      position: relative;
      .app-logo {
        width: 16px;
        height: 16px;
        margin: 8px 0 0 8px;
      }
      > span {
        font-size: 15px;
        color: white;
        line-height: 20px;
        vertical-align: 3px;
        margin-left: 10px;
        padding-top: 5px;
        display: inline-block;
      }
      .drag-area {
        position: absolute;
        left: 0;
        top: 0;
        width: calc(100% - 48px);
        height: 32px;
        -webkit-app-region: drag;
      }
      .close {
        position: absolute;
        right: 0;
        top: 0;
        width: 46px;
        height: 28px;
        cursor: pointer;
        line-height: 28px;
        text-align: center;
        &:hover {
          background: #688ddc;
        }
        > img {
          width: 11px;
          height: 11px;
          filter: invert(100%);
          margin-top: 10px;
        }
      }
    }

    .add-prop-main {
      box-sizing: border-box;
      padding: 24px 9px 20px 13px;

      .code-table {
        box-sizing: border-box;
        max-height: 444px;
        overflow-y: auto;
        overflow-x: hidden;
        &::-webkit-scrollbar {
          // background: #ccc;
          background: transparent;
          width: 0px;
        }
        /*定义滚动条轨道 内阴影+圆角*/
        // &::-webkit-scrollbar-track
        // {
        //     -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
        //     border-radius: 10px;
        //     background-color: #F5F5F5;
        // }

        /*定义滑块 内阴影+圆角*/
        &::-webkit-scrollbar-thumb {
          -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.7);
          // background-color: #555;
          background: #ccc;
        }
      }

      .btns-wrap {
        text-align: right;
        > button {
          width: 81px;
          height: 23px;
          background: rgba(230, 230, 230, 1);
          font-size: 12px;
          color: rgba(102, 102, 102, 1);
          line-height: 21px;
        }
        .back {
          margin-right: 18px;
          border: 1px solid #999;
          color: #666;
        }
        .hold {
          border: 1px solid #2249c0;
          color: #2249c0;
        }
      }
    }
  }
}
.row-cell {
  font-size: 12px;
  padding-bottom: 20px;
  box-sizing: border-box;
  position: relative;
  &.error input[type='text'],
  &.error input[type='number'],
  &.error textarea {
    border-color: red;
  }
  label {
    width: 84px;
    color: rgba(30, 29, 29, 1);
    line-height: 27px;
    text-align: right;
  }
  .item-wrap {
    display: flex;
    width: 90%;
  }
  .message {
    position: absolute;
    right: 10%;
  }
  .in-wrap {
    flex: 1;
  }

  input[type='text'],
  input[type='number'] {
    font-size: 12px;
    width: 100%;
    height: 27px;
    border-radius: 2px;
    border: 1px solid #d8d8d8;
    box-sizing: border-box;
    text-align: left;
    padding: 0 4px;
    color: #666;
    &:focus {
      border: 1px solid rgba(80, 118, 200, 1);
    }
  }
  textarea {
    width: 100%;
    height: 52px;
    border-radius: 2px;
    border: 1px solid #d8d8d8;
    box-sizing: border-box;
    text-align: left;
    padding: 4px;
    color: #666;
    resize: none;
    &:focus {
      border: 1px solid rgba(80, 118, 200, 1);
    }
  }
  select {
    width: 100%;
    height: 27px;
    border-radius: 2px;
    border: 1px solid #d8d8d8;
    box-sizing: border-box;
    outline: none;
    &:focus {
      border: 1px solid rgba(80, 118, 200, 1);
    }
  }
  input[type='radio'] {
    vertical-align: middle;
    margin: 0 5px 0 0;
  }

  .t-row {
    margin-bottom: 2px;
    > input[type='checkbox'],
    > input[type='radio'] {
      margin: 0 4px 0 0;
      vertical-align: middle;
      height: 15px;
    }
    > input[type='text'] {
      width: 81px;
      height: 25px;
      line-height: 25px;
      padding-left: 2px;
      border-radius: 2px;
      border: 1px solid #d8d8d8;
      box-sizing: border-box;
      margin-right: 4px;
      text-align: left;
      &:focus {
        border: 1px solid rgba(80, 118, 200, 1);
      }
    }
    > button {
      width: 14px;
      height: 16px;
      background-size: 100% 100%;
      vertical-align: middle;
      border: none;
      padding: 0;
    }
    > button.add {
      background: url(./assets/extend/icon-add-button.png) no-repeat;
    }
    > button.del {
      background: url(./assets/extend/order_del_icon_noactive.png) no-repeat;
    }
  }

  .tip-wrap {
    width: 14px;
    height: 14px;
    position: absolute;
    top: 7px;
    right: 10px;
    .icon {
      width: 14px;
      height: 14px;
      > img {
        width: 100%;
        height: 100%;
      }
    }
    .tip-cell {
      display: none;
      position: absolute;
      box-sizing: border-box;
      padding: 3px 0 3px 5px;
      border-radius: 2px;
      border: 1px solid #999;
      left: -143px;
      top: 26px;
      background: white;
      z-index: 10;
      &:before {
        content: '';
        display: inline-block;
        width: 8px;
        height: 8px;
        border-left: 1px solid #999;
        border-top: 1px solid #999;
        background: white;
        transform: rotate(45deg);
        position: absolute;
        left: 144px;
        top: -6px;
      }
      > p {
        margin: 0;
        line-height: 18px;
        font-size: 12px;
        color: #666;
        // max-width: 150px;
        width: 150px;
      }
    }
    &:hover {
      .tip-cell {
        display: block;
      }
    }
  }
}
</style>
