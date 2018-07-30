# vscode 编译less -> wxss

现在微信开发工具太多，想微信原生的开发方式来开发。但是写样式又太繁还是想借助一些预编译的方式来方便开发，于是乎就Easy LESS 就非常适合了。

1. 开发工具: [vscode](https://code.visualstudio.com/)
2. 插件安装: [Easy LESS](https://marketplace.visualstudio.com/items?itemName=mrcrowl.easy-less#overview)

配置参考地址: [https://marketplace.visualstudio.com/items?itemName=mrcrowl.easy-less#overview](https://marketplace.visualstudio.com/items?itemName=mrcrowl.easy-less#overview)


+ Easy LESS扩展配置全局配置，全局设置 out 为 false, 避免与自己的其他项目编译工具有冲突。

```json
{
  "less.compile": {
    "out":       false, // false => DON'T output .css files (overridable per-file, see below)
    "compress":  true,  // true => remove surplus whitespace
    "sourceMap": false,  // true => generate source maps (.css.map files)
    "autoprefixer": "> 5%, last 2 Chrome versions, not ie 6-9", // 取值参考: autoprefixer & browserslist配置
  },
}
```

+ 文件机构 & 页面(例app.less)添加设置

```
└── src
    ├── app.js
    ├── app.json
    ├── app.wxss
    ├── app.less
```

`app.less` 文件内顶部添加 `// out: app.wxss`这样在保存时候插件会自动将 app.less 内容编译到 app.wxss 文件中。同理其他的页面也是类似的(在同级的页面添加一个输出设置)，这样在写样式的时候就可以使用less相关语法，也可以提高开发效率。