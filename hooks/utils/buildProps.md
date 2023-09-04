## fromPairs
先看 `toPairs` 
```js
_.toPairs([['a', 1], ['b', 2], ['c', 3]]);
=> {'a': 1, 'b': 2, 'c': 3}
```

`fromPairs`作用则反过来
- returns key-value 'paris' composed from an object

## buildProp
```ts
(val) =>
  fromPairs(
    Object.entries(props).map(([key, option]) => [
      key,
      buildProp(option as any, key),
    ])
  )
```
将对象转为数组 `[key, option]`, 然后用 option 去 buildProp，得到 key 对应的在 vueProps 下的合法属性约束

## buildProps
```ts
(prop, key) => {
  if (!isObject(prop) || isEpProp(prop)) return prop as any

  const { values, required, default: defaultValue, type, validator } = prop

  // 如果 values 存在取 values
  // 如果 validator 存在 进行校验
  // 不通过则抛出 warn
  // 都不存在给 validator 赋值 undefined
  const _validator =
    values || validator
      ? (val: unknown) => {
          let valid = false
          let allowedValues: unknown[] = []

          if (values) {
            allowedValues = Array.from(values)
            if (hasOwn(prop, 'default')) {
              allowedValues.push(defaultValue)
            }
            valid ||= allowedValues.includes(val)
          }
          if (validator) valid ||= validator(val)

          if (!valid && allowedValues.length > 0) {
            const allowValuesText = [...new Set(allowedValues)]
              .map((value) => JSON.stringify(value))
              .join(', ')
            warn(
              `Invalid prop: validation failed${
                key ? ` for prop "${key}"` : ''
              }. Expected one of [${allowValuesText}], got value ${JSON.stringify(
                val
              )}.`
            )
          }
          return valid
        }
      : undefined

  const epProp: any = {
    type,
    required: !!required,
    validator: _validator,
    [epPropKey]: true, // TODO: 这个参数的作用?
  }
  if (hasOwn(prop, 'default')) epProp.default = defaultValue
  return epProp
}
```