# 这里介绍官方react脚手架引入antd并修改主题样式
1.  安装官网脚手架
`npm install -g create-react-app yarn`
2.  创建自己的项目  
`create-react-app antd-demo`
3.  进入项目并启动项目  
```
  $ cd antd-demo
  $ yarn start
```
4.  安装antd  
`yarn add antd`
5.  将所有内建的配置暴露出来 
`yarn run eject`
6. babel-plugin-import 是一个用于按需加载组件代码和样式的 babel 插件
`yarn add babel-plugin-import --dev`

7.  修改 config/webpack.config.dev.js
```
    {
      test: /\.(js|jsx)$/,
      include: paths.appSrc,
      loader: require.resolve('babel-loader'),
      options: {
    +   plugins: [
    +     ['import', { libraryName: 'antd', style: 'css' }],
    +   ],
        // This is a feature of `babel-loader` for webpack (not Babel itself).
        // It enables caching results in ./node_modules/.cache/babel-loader/
        // directory for faster rebuilds.
        cacheDirectory: true
      }
    },
```
8.  然后移除前面在 src/App.css 里全量添加的 @import '~antd/dist/antd.css'; 样式代码，并且按下面的格式引入模块。在需要的地方引入
`import { Button } from 'antd';`
9.  我们需要引入 less-loader 来加载 less 样式  
`yarn add less less-loader --dev`
`exclude: [/\.js$/, /\.html$/, /\.less$/, /\.json$/], // 加入less匹配`
10. config/webpack.config.dev.js 修改并整段加入下面代码
```
    options: {
      plugins: [
-       ['import', [{ libraryName: 'antd', style: 'css' }]],
+       ['import', [{ libraryName: 'antd', style: true }]],  // import less
      ],
    },
```
```
    {
      test: /\.less$/,
      use: [
        require.resolve('style-loader'),
        require.resolve('css-loader'),
        {
          loader: require.resolve('postcss-loader'),
          options: {
            ident: 'postcss', // https://webpack.js.org/guides/migrating/#complex-options
            plugins: () => [
              require('postcss-flexbugs-fixes'),
              autoprefixer({
                browsers: [
                  '>1%',
                  'last 4 versions',
                  'Firefox ESR',
                  'not ie < 9', // React doesn't support IE8 anyway
                ],
                flexbox: 'no-2009',
              }),
            ],
          },
        },
        {
          loader: require.resolve('less-loader'),
          options: {
            modifyVars: { "@primary-color": "#ff6600" }, // 这里就是你需要配置的样式了
          },
        },
      ],
    },
```