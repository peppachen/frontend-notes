## React

### useDebounce Hook

```tsx
// useDebounce.ts
import { useEffect, useState } from "react";

export function useDebounce<T>(value: T, delay = 500): T {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}
//使用

const [input, setInput] = useState("");
const debouncedInput = useDebounce(input, 500);

useEffect(() => {
  if (debouncedInput) {
    // 发起搜索请求
  }
}, [debouncedInput]);
```

### useThrottle Hook

```tsx
// useThrottle.ts
import { useState, useEffect } from "react";

export function useThrottle<T>(value: T, delay = 500): T {
  const [throttledValue, setThrottledValue] = useState(value);
  const [lastTime, setLastTime] = useState(Date.now());

  useEffect(() => {
    const now = Date.now();
    if (now - lastTime >= delay) {
      setThrottledValue(value);
      setLastTime(now);
    }
  }, [value, delay, lastTime]);

  return throttledValue;
}
```

## Vue

### debounce

```ts
// debounceDirective.ts
export default {
  mounted(el: HTMLElement, binding: any) {
    let timer: any;
    el.addEventListener("input", (e: Event) => {
      clearTimeout(timer);
      timer = setTimeout(() => {
        binding.value(e);
      }, binding.arg || 500);
    });
  },
};
//使用
<input v-debounce:300="handleInput" />;
//注册
app.directive("debounce", debounceDirective);
```

## Lodash 三方插件

```JS
// 安装 npm/pnpm/yarn install loadsh
import debounce from 'lodash/debounce';
import throttle from 'lodash/throttle';

const debouncedFn = debounce(() => {
  console.log('防抖触发');
}, 300);

const throttledFn = throttle(() => {
  console.log('节流触发');
}, 1000);

```
