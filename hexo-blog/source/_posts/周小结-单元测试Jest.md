---
title: 周小结-单元测试Jest
date: 2017-11-13 20:05:01
tags: 周小结
---
# 在Vue项目中进行jest单元测试
## 配置
```
$ npm install --save-dev vue-test-utils
// 官方推荐

$ npm install jest jest-vue-preprocessor --save-dev
// jest-vue-preprocessor：是jest的一个插件，用来解析’.vue’文件的

$ npm install --save-dev jest-serializer-vue
// 测试快照

```
<!-- more -->
### package.json中创建 jest 块
文件最底部添加：
```
{
  // ...
  "jest": {
    // 配置文件拓展名
    "moduleFileExtensions": [
      "js",
      "json",
      // 告诉 Jest 处理 `*.vue` 文件
      "vue"
    ],
    // 匹配webpack中配置的alias
    "moduleNameMapper": {
      "^vue$": "vue/dist/vue.common.js",
      // 把 @ 设置为 /src 的别名
      "^@(.*)$": "<rootDir>/src$1"
    },
    // 编译工具
    "transform": {
      // 用 `jest-vue-preprocessor` 处理 `*.vue` 文件
      ".*\\.(vue)$": "<rootDir>/node_modules/jest-vue-preprocessor",
      // 用 `babel-jest` 处理 js
      ".*": "babel-jest"
    },
    "mapCoverage": true,
    // 快照的序列化程序
    "snapshotSerializers": [
      "<rootDir>/node_modules/jest-serializer-vue"
    ]
  }
}
```
注意：package.json文件中不能有注释存在！！！

script中添加一条新的命令
```
{
  // 删除
  "test": "npm run unit && npm run e2e",
  // ...
  "test": "./node_modules/.bin/jest"
}
```
至此，配置完成！！！

# 第一个简单的单元测试
项目中存在一个header.vue组件，创建～／text／unit/specs/header.spec.js文件：
```
import Header from '@/components/header/header';
import { mount } from 'vue-test-utils';

describe('Header Component', () =>{
    test('取值测试', () => {
        const wrapper = mount(Header);
        expect(wrapper.text()).toEqual('电影 ');
        console.log('取值测试成功！');
    });
});
```

# jest知识
## 匹配器
toBe：使用===精确匹配。
toEqual：递归检查对象或数组的每个字段。
toNull：只匹配 null。
toBeUndefined：只匹配 undefined。
toBeDefined：与 toBeUndefined 相反。
toBeTruthy：匹配任何 if 语句为真。
toBeFalsy：匹配任何 if 语句为假。
toBeCloseTo：比较浮点数相等。
toMatch：正则表达式的字符串。
toContain：检查数组是否包含特定子项。
toThrow：测试的特定函数抛出一个错误。

## 与vuex一起使用
为了完成这个测试，我们需要在浅渲染组件时给 Vue 传递一个伪造的 store。
我们可以把 store 传递给一个 localVue，而不是传递给基础的 Vue 构造函数。
localVue 是一个独立作用域的 Vue 构造函数，我们可以对其进行改动而不会影响到全局的 Vue 构造函数。
```
import { shallow, createLocalVue } from 'vue-test-utils';
import Vuex from 'vuex';
import Listview from '@/components/listview/listview';

const localVue = createLocalVue();

localVue.use(Vuex);

describe('Listview.vue', () => {
    test('当按钮被点击时候调用“actionClick”的 action', () => {
        const wrapper = shallow(Actions, { store, localVue });
        // .....本周继续
    });
});
```


