---
title: AntD组件在modal内，Form警告警告情况
date: 2024-07-02 20:27:25
tags:
---

#### 现象

使用 AntD 组件，如果 Modal 弹框使用 Form 表单组件容易提示：

```javascript
Warning: Instance created by useForm is not connected to any Form element.
Forget to pass form prop?
```

<!--more-->

#### 原因

一般情况 Form 在 Modal 内使用不会有问题，但是如果在 modal 弹框展示前，提前使用了 form 对象属性，就会有 Form 组件未关联 form 实例属性的警告。如下示意代码（仅演示，不具实用性）

![image](https://github.com/xkrabb/xkrabb.github.io/assets/8550870/6e8828ab-e44b-4788-9e7e-7468810c11f3)

#### 处理方式

- 强制 Form 渲染，Modal 组件提供 forceRender 属性，强制弹框内组件渲染。（但不会显示）
- 不要提前使用 from 实例属性，如上截图，应该避免 form 作为依赖。
- 错误信息应该是在值更新才会处理，所以更好的方式是在 onValueChange 回调函数里获取；
- 如果真要提前获取错误信息，那也是应该依赖 modal 的 open 状态，在打开之后再获取；
