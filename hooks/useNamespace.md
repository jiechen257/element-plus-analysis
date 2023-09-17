主要是返回一系列的 class 类名

## 概念
class 类名组成：namespace + block + blockSuffix + element + modifier, 其中
- b - block
- e - element
- m - modifier

## 方法
```js
// useNamespace
export const useNamespace = (block, namespaceOverrides) => {
  return {
    namespace, // 根据 useGetDerivedNamespace 得到当前 vue 实例的 nameSpace,
    b, // 在 class 组成中只添加 block
    e, // 同上，只添加 element
    m,
    be,
    em,
    bm,
    bem,
    is,
    cssVar, // 用 namespace 包一层，转为 cssVar 对象
    cssVarName,
    cssVarBlock,
    cssVarBlockName,
  }
}
```

```js
// useGetDerivedNamespace
export const useGetDerivedNamespace = (
  namespaceOverrides?: Ref<string | undefined>
) => {
  const derivedNamespace =
    namespaceOverrides ||
    (getCurrentInstance()
      ? inject(namespaceContextKey, ref(defaultNamespace)) // vue 实例存在则注入 namespace 变量
      : ref(defaultNamespace)) // vue 实例不存在取默认值 —— 'el'
  const namespace = computed(() => {
    return unref(derivedNamespace) || defaultNamespace
  })
  return namespace
}
```
