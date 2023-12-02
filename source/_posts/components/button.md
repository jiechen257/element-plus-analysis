```html
<component :is="props.tag">
  <template v-if="loading"></template>
  <el-icon v-else-if="props.icon">
  <span v-if="$slot.default"></span>
</component>
```

## 探讨
### $slot.default 的值

当渲染一个默认的 el-button 时，内部会带上 span 元素

可 $slot.default 的值应当是空，为何 span 会被渲染
```html
<template>
  <el-button>
    <!-- 空内容 -->
  {{ $slots.default }}
  </el-button>
</template>
```

![](https://cdn.jsdelivr.net/gh/jiechen257/gallery@main/img/202312011720038.png)

![](https://cdn.jsdelivr.net/gh/jiechen257/gallery@main/img/202312011720716.png)

```js
// $slots.default 真实值
  (...args) =&gt; {
    if (renderFnWithContext._d) {
      setBlockTracking(-1);
    }
    const prevInstance = setCurrentRenderingInstance(ctx);
    const res = fn(...args);
    setCurrentRenderingInstance(prevInstance);
    if (renderFnWithContext._d) {
      setBlockTracking(1);
    }
    if (true) {
      devtoolsComponentUpdated(ctx);
    }
    return res;
  } 
```

`$slots.default !== null`, 然后 v-if 生效，然后 $slot 在 `{{}}` 里被解析成 `""` 空字符串，所以span元素内为空

