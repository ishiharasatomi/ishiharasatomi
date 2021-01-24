## Vue3.0光速上手

### teleport

传送门组件提供一种简洁的方式可以指定它里面内容的父元素

```vue
<template>
  <div>
    <button @click="handleModelOpen">打开模态窗</button>
    <teleport to="body">
      <div class="model" v-if="show">
        <p>this is a model</p>
        <button @click="handleModelClose">关闭模态窗</button>
      </div>
    </teleport>
  </div>
</template>

<script>
import { ref } from "vue";
export default {
  setup() {
    const show = ref(false);
    const handleModelOpen = () => {
      show.value = true;
    };
    const handleModelClose = () => {
      show.value = false;
    };
    return {
      show,
      handleModelOpen,
      handleModelClose,
    };
  },
};
</script>

<style>
.model{
    width: 600px;
    height: 400px;
    background-color: #323142;
    border: 1px solid #999;
    margin: 200px auto;
    text-align: center;
    position: absolute;
    left: 50%;
    top: 10%;
    transform: translate(-50%,-50%);
}
</style>
```

### Fragments

vue3中组件可以拥有多个根。

```vue
<template>
  <FHeader />
  <FMain />
  <FFooter />
</template>

<script>
import FHeader from "./Fragments/FHeader.vue";
import FMain from "./Fragments/FMain.vue";
import FFooter from "./Fragments/FFooter.vue";

export default {
    components: {
        FHeader,
        FMain,
        FFooter
    }
};
</script>
```

### Emits Component Option

vue3中组件发送的自定义事件需要定义在emits选项中：

+ 原生事件会触发两次，比如click
+ 更好的指示组件工作方式

`Child.vue`

```vue
<template>
  <div class="emits-option">
    <button @click="$emit('custom-check')">click</button>
  </div>
</template>

<script>
export default {
  name: "emit-option",
  emits: ['custom-check']
};
</script>
```

`Father.vue`

```vue
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <EmitsOption @custom-check="handleCheck"/>
  </div>
</template>

<script>
import ModelButton from "./ModelButton";
export default {
  name: "HelloWorld",
  components: {
    EmitsOption
  },
  setup(){
    function handleCheck(){
      console.log('checked~');
    }
    return {
      handleCheck
    }
  }
};
</script>
```

### Global API改为应用程序实例调用

vue3中使用`createApp`返回`App`实例，由它暴露全局api。可以扩展到其他全局API。

`main.js`

```javascript
import { createApp, h } from "vue";
import App from "./App.vue";
import router from "./router";
import store from "./store";

createApp(App)
  // 全局api通过实例暴露
  .component("global-component", {
    render() {
      return h("h1", {}, "this is a global-component created by h");
    },
  })
  .use(store)
  .use(router)
  .mount("#app");

```

`HelloWorld.vue`

```vue
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <global-component></global-component>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
};
</script>
```

### API摇树优化

vue2中不少global-api是作为**静态函数**直接挂在构造函数上的，例如`Vue.nextTick()`，如果我们从未在代码中用过它们，就会形成所谓的`dead code`，这类global-api造成的`dead code`无法使用webpack的tree-shaking排除掉。

```js
import Vue from 'vue'

Vue.nextTick(() => {
  // something something DOM-related
})
```

vue3中做了相应的变化，将它们抽取成为独立函数，这样打包工具的摇树优化可以将这些dead code排除掉。

```js
import { nextTick } from 'vue'

nextTick(() => {
  // something something DOM-related
})
```

### v-model使用的变化

model选项和v-bind的sync修饰符被移除，统一为v-model参数形式。vue2中.sync和v-model功能有重叠，容易混淆，vue3做了统一。

```vue
<template>
  <div class="hello">
    <CustomInput v-model="searchText" />
    <!-- <CustomInput
      :model-value="searchText"
      @update:model-value="searchText = $event"
    /> -->
  </div>
</template>

<script>
import CustomInput from "./CustomInput";
import { ref } from "vue";
export default {
  components: {
    CustomInput,
  },
  setup() {
    const searchText = ref("");
    return {
      searchText,
    };
  },
};
</script>
```

`CustomInput.vue`

```vue
<template>
  <div class="custom-input">
    <input
      type="text"
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)"
    />
    <p>{{ modelValue }}</p>
  </div>
</template>

<script>
export default {
  props: {
    modelValue: {
      type: String,
    },
  },
};
</script>
```

### 渲染函数API修改

渲染函数发生变化，更简洁。

+ h函数不再作为参数传递到render函数中，单独导入，利于摇树
+ 利用闭包特性，可以使用setup中定义的响应式状态和函数

#### 2.x

```javascript
export default {
  render(h) {
    return h('div')
  }
}
```

#### 3.x

```javascript

import { h, ref } from 'vue'
export default {
    setup(){
        const message = ref("have a nice day~");
        const handlePrint = () => {
            console.log(message.value)
        }
        return () => {
            return h(
                "button",
                {
                    onClick: handlePrint
                },
                "click me!"
            )
        }
    }
}
```

