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

### 自定义渲染器

