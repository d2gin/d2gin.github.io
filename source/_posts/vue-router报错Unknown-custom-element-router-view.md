---
title: vue-router报错Unknown custom element router-view
date: 2020-04-02 10:33:13
index_img: /cover/89ee1b868aa29359c8b0c81918cd7eb1.jpeg
banner_img: /cover/89ee1b868aa29359c8b0c81918cd7eb1.jpeg
tags:
	- vue
	- vue-router
---

很久没用过vue了，以前用得也不多，到现在差不多都忘光了。

项目main.js

```vue
import Vue from 'vue'
import App from './App.vue'
import VueRouter from "vue-router";

const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }
const routes = [
    {path: '/Foo', component: Foo},
    {path: '/Bar', component: Bar}
]
const router = new VueRouter({
    routes // (缩写) 相当于 routes: routes
})

Vue.config.productionTip = false
new Vue({
    render: h => h(App),
    router,
}).$mount('#app')
```

报错

> vue.runtime.esm.js?2b0e:619 [Vue warn]: Unknown custom element: <router-link> - did you register the component correctly? For recursive components, make sure to provide the "name" option.
>
> found in
>
> ---> <App> at src/App.vue
>        <Root>

检索报错得知是没有``Vue.use(VueRouter)``把代码加入main.js即可