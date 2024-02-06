#typescript 

需要安装 ts-loader

# 安装 ts-loader

`npm i -D ts-loader`

# 配置文件

可以使用 `npx webpack init` 启动向导来生成配置文件

## webpack.config.js

```js
const path = require('path');

module.exports = {
  entry: './src/index.ts',
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
  resolve: {
    extensions: ['.tsx', '.ts', '.js'],
  },
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
};
```

[TypeScript | webpack 中文文档 (docschina.org)](https://webpack.docschina.org/guides/typescript/#root)

