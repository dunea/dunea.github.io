---
title: 如何在js/ts中使用tailwindcss的屏幕断点【附带代码】
date: 2024-10-09 23:51:19
categories:
    - WEB
tags:
    - tailwindcss
---


### js / ts代码

在ts项目中直接导入 `../../tailwind.config.js` 可能会报错找不到类型，直接使用 `// @ts-ignore` 忽略即可。

<!-- more -->

```js
import { useEffect, useState } from "react";
import resolveConfig from "tailwindcss/resolveConfig";
import tailwindConfig from "../../tailwind.config.js";

class Display {
  xs: boolean | undefined;
  sm: boolean | undefined;
  md: boolean | undefined;
  lg: boolean | undefined;
  xl: boolean | undefined;
  xxl: boolean | undefined;
  smAndDown: boolean | undefined;
  smAndUp: boolean | undefined;
  mdAndDown: boolean | undefined;
  mdAndUp: boolean | undefined;
  lgAndDown: boolean | undefined;
  lgAndUp: boolean | undefined;
  xlAndDown: boolean | undefined;
  xlAndUp: boolean | undefined;
  xxlAndDown: boolean | undefined;
  xxlAndUp: boolean | undefined;


  constructor(width: number) {
    this.initVariable();
    this.calcVariable(width);
  }

  initVariable() {
    this.sm =
      this.md =
      this.lg =
      this.xl =
      this.xxl =
      this.smAndDown =
      this.smAndUp =
      this.mdAndDown =
      this.mdAndUp =
      this.lgAndDown =
      this.lgAndUp =
      this.xlAndDown =
      this.xlAndUp =
      this.xxlAndDown =
      this.xxlAndUp =
      false;
  }

  calcVariable(width: number) {
    const fullConfig = resolveConfig(tailwindConfig)
    const breakpoints = {
      sm: parseInt(fullConfig.theme.screens.sm),
      md: parseInt(fullConfig.theme.screens.md),
      lg: parseInt(fullConfig.theme.screens.lg),
      xl: parseInt(fullConfig.theme.screens.xl),
      xxl: parseInt(fullConfig.theme.screens["2xl"]),
    };

    if (width < breakpoints.sm) {
      this.xs = true;
    } else if (width < breakpoints.md) {
      this.sm = true;
    } else if (width < breakpoints.lg) {
      this.md = true;
    } else if (width < breakpoints.xl) {
      this.lg = true;
    } else if (width < breakpoints.xxl) {
      this.xl = true;
    } else {
      this.xxl = true;
    }

    this.smAndDown = this.xs || this.sm; // < 768
    this.smAndUp = this.sm || this.md || this.lg || this.xl || this.xxl; // > 640
    this.mdAndDown = this.xs || this.sm || this.md; // < 1024
    this.mdAndUp = this.md || this.lg || this.xl || this.xxl; // > 768
    this.lgAndDown = this.xs || this.sm || this.md || this.lg; // < 1280
    this.lgAndUp = this.lg || this.xl || this.xxl; // > 1024
    this.xlAndDown = this.xs || this.sm || this.md || this.lg || this.xl; // < 1536
    this.xlAndUp = this.xl || this.xxl; // > 1280
    this.xxlAndDown = this.xs || this.sm || this.md || this.lg || this.xl; // < 1536
    this.xxlAndUp = this.xxl; // > 1536
  }
}

function useDisplay() {
  const [display, setDisplay] = useState(new Display(window.screen.width));

  useEffect(() => {
    function handleResize(e: any) {
      // 可考虑防抖
      setDisplay(new Display(e?.target.innerWidth));
    }

    window.addEventListener("resize", handleResize);

    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);

  return display;
}

export default useDisplay;
```


### 使用

```js
const display = useDisplay();

console.log(display.sm);
```

这样使用，就可以在js / ts中使用tailwindcss的屏幕断点了。

功能参照的 VUETIFY 的 `useDisplay` 实现。

### 参考

- [VUETIFY 的 useDisplay](https://vuetifyjs.com/zh-Hans/features/display-and-platform/#section-63a553e3)
- [tailwindcss 的 resolveConfig](https://github.com/tailwindlabs/tailwindcss/blob/master/src/util/resolveConfig.js)
