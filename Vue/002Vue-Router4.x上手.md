## Vue-Router4.x新特性

vue-router4保持了大部分API不变，只关注变化部分。

### 挂载插件

使用vue-router中`createRouter`api去创建router实例

`router.js`

```javascript
import { createRouter, createWebHistory } from 'vue-router'
import Home from '../views/Home.vue'

const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/about',
    name: 'About',
    component: () => import(/* webpackChunkName: "about" */ '../views/About.vue')
  }
]

const router = createRouter({
  history: createWebHistory(process.env.BASE_URL),
  routes
})
export default router
```

在`main.js`中的app实例上挂载路由器。

`main.js`

```javascript
import { createApp, h } from "vue";
import App from "./App.vue";
import router from "./router";

createApp(App)
  .use(router)
  .mount("#app");
```

### 动态添加路由

通过路由器实例动态添加路由。

`router.js`

```javascript
import { createRouter, createWebHistory } from 'vue-router'
import Home from '../views/Home.vue'

const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/about',
    name: 'About',
    // route level code-splitting
    // this generates a separate chunk (about.[hash].js) for this route
    // which is lazy-loaded when the route is visited.
    component: () => import(/* webpackChunkName: "about" */ '../views/About.vue')
  }
]

const router = createRouter({
  history: createWebHistory(process.env.BASE_URL),
  routes
})

// 动态添加路由
router.addRoute({
  path: '/contact',
  name: 'Contact',
  component: () => import(/* webpackChunkName: "contact" */ '../views/Contact.vue')
})

// 根据名字动态添加子路由
router.addRoute('Contact',{
  path: "/contact/email",
  name: "Email",
  component: () => import(/* webpackChunkName: "Email" */ '../components/Contact/Email.vue')
})
router.addRoute('Contact',{
  path: "/contact/phone",
  name: "Phone",
  component: () => import(/* webpackChunkName: "Phone" */ '../components/Contact/Phone.vue')
})

export default router
```

`Contact.vue`

```vue
<template>
  <div class="contact">
      <h2>contact us! page</h2>
      <nav>
          <router-link to="/contact/email">Email</router-link>
          <router-link to="/contact/phone">Phone</router-link>
      </nav>
      <router-view></router-view>
  </div>
</template>
```

### 路由钩子

#### useRouter

在composition中我们需要动态跳转时，可以通过提供`useRouter`的hook获取router实例操作路由器。

```vue
<template>
  <div class="hello">
    <div>
      <button @click="handleGoToContact">To Contact Page</button>
    </div>
  </div>
</template>

<script>
import { onBeforeRouteLeave, useRouter } from "vue-router";
export default {
  setup() {
    // 通过useRouter函数获取router实例
    const router = useRouter();
      
    function handleGoToContact() {
      // 操作路由器
      router.push({
        path: "/contact/phone",
      });
    }
    // 路由守卫
    onBeforeRouteLeave(() => {
      let answer = window.confirm(
        "Something unsave.Are you sure leave this page?"
      );
      if (!answer) {
        return false;
      }
    });
    return {
      handleGoToContact,
    };
  },
};
</script>
```