### 依赖库
`async-validator` 用于验证

`vue-property-decorator` 用于简化vue组件写法

## UbForm使用方法

类似antd表单使用

### 引入必要组件
1、ub-form

2、ub-formItem

其他受控组件按需加入、按需求秀改

### 初始化
1、类似原生form包含
```html
<Ub-Form>
    <Ub-FormItem>
        ···
        <Ub-Input />
        ···
    </Ub-FormItem>
</Ub-Form>
```

2、定义`<Ub-Form>`的model，如formDate，可以为空对象，也可以赋值 -- 即初始化，编辑的时候也直接赋值给这个model


```html
<Ub-Form ref="form" :model="formData" :rules="rules" @submit="handleSubmit">
    <Ub-FormItem label="参数名称：" prop="parmaEnName" class="row-cell">
        <Ub-Input type="text" placeholder="调用的参数英文名称" />
    </Ub-FormItem>
</Ub-Form>
```
3、定义rules数组 用于写每个字段的验证规则，如

这里使用的`async-validator`库做验证功能，antd、element也是使用的它

```js
rules = {
    parmaEnName: [
        { required: true, message: "参数名称不能为空", trigger: "change" }
    ]
}
```

4、submit方法为最后提交按钮触发`button type=submit`，即可拿到干净的form数据

5、`<Ub-FormItem>`中定义 `prop` 即字段key，里面的input无需再使用v-model双绑。


### 后语

在没有第三方UI库的情况下，写form 一般使用 v-model 绑定每个受控div（如input、select等）,但<br />
这样写的坏处显而易见，`乱`、`臃肿`、`拿值各种判断`，最重要的是碰到验证、编辑功能更是n多判断。<br />
所以 这种类antd的表单写法就可以解决这些问题，每个组件做自己的事、值也全在form一个地方。


### 在线查看效果

<iframe
    src="https://codesandbox.io/embed/silly-hertz-k6x2h?fontsize=14&hidenavigation=1&theme=dark"
    style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
    title="silly-hertz-k6x2h"
    allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb"
    sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"
></iframe>