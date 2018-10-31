---
title: 在现有Vue项目中引入React
tags:
  - Vue
  - React
  - webpack
categories:
  - 前端
date: 2018-07-01 15:24:19
---

## 安装相应依赖以及转码打包的工具

运行以下代码
```bash
  npm install  --save-dev react react-dom babel-cli babel-preset-react
```
将依赖写入package.json文件中，如下：
```bash
  "react": "^16.4.1",
  "react-dom": "^16.4.1",
  "babel-cli": "^6.26.0",
  "babel-preset-react": "^6.24.1",
```

## 修改**.babelrc**文件如下：
```bash
  {
    "presets": [
      ["env", {
        "modules": false,
        "targets": {
          "browsers": ["> 1%", "last 2 versions", "not ie <= 8"]
        }
      }],
      "stage-2",
      "react"
    ],
    "plugins": ["transform-runtime"],
    "comments": false,
    "env": {
      "test": {
        "presets": ["env", "stage-2","react"],
        "plugins": [ "istanbul" ]
      }
    }
  }
```

## 最后在Vue文件中引入react相应组件

    import xx.js from ./xx

  