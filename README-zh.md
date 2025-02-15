[English](./README.md) | 简体中文

## webpack-to-vite
将 webpack 项目转换为 vite 项目

## 快速开始

1. 下载
```
git clone https://github.com/originjs/webpack-to-vite.git
cd webpack-to-vite
```
2. 安装

使用 npm, 运行
```
npm install
npm run build
```
使用 yarn, 运行
```
yarn
yarn build
```
3. 转换
```
node ./bin/index -d <project path>
```

## 演示项目

以下是使用工具成功从 webpack 项目转换为 vite 项目的项目列表

- [helloworld-vue2](https://github.com/originjs/webpack-to-vite-demos/tree/main/helloworld-vue2)
- [helloworld-vue3](https://github.com/originjs/webpack-to-vite-demos/tree/main/helloworld-vue3)
- [helloworld-webpack](https://github.com/originjs/webpack-to-vite-demos/tree/main/helloworld-webpack)
- [vue-manage-system-vite](https://github.com/originjs/webpack-to-vite-demos/tree/main/vue-manage-system-vite)
- [newbee-mall-vue3-app-vite](https://github.com/originjs/webpack-to-vite-demos/tree/main/newbee-mall-vue3-app-vite)
- [vue-realworld-example-app-vite](https://github.com/originjs/webpack-to-vite-demos/tree/main/vue-realworld-example-app-vite)
- [vue-uploader-vite](https://github.com/originjs/webpack-to-vite-demos/tree/main/vue-uploader-vite)
- [douban-vite](https://github.com/originjs/webpack-to-vite-demos/tree/main/douban-vite)

## 成功转换的项目

### vue-cli
- [vue-manage-system](https://github.com/lin-xin/vue-manage-system) -> [vue-manage-system-vite](https://github.com/originjs/webpack-to-vite-demos/tree/main/vue-manage-system-vite)
- [newbee-mall-vue3-app](https://github.com/newbee-ltd/newbee-mall-vue3-app) -> [newbee-mall-vue3-app-vite](https://github.com/originjs/webpack-to-vite-demos/tree/main/newbee-mall-vue3-app-vite)
- [vue-realworld-example-app](https://github.com/gothinkster/vue-realworld-example-app) -> [vue-realworld-example-app-vite](https://github.com/originjs/webpack-to-vite-demos/tree/main/vue-realworld-example-app-vite)

### webpack
- [vue-uploader](https://github.com/simple-uploader/vue-uploader) -> [vue-uploader-vite](https://github.com/originjs/webpack-to-vite-demos/tree/main/vue-uploader-vite)
- [douban](https://github.com/jeneser/douban) -> [douban-vite](https://github.com/originjs/webpack-to-vite-demos/tree/main/douban-vite)

## 转换项
以下是需要转换的配置项列表

图例注解：

| 图例 | 描述 |
| ---- | ---- |
|✅|通过 `webpack-to-vite` 自动转换|
|⚠️|需要手动转换|
|❌|目前不支持|

### 基础转换项
* ✅ B01: 在 `package.json` 中添加必要的 devDependencies 和 dependencies
  * 必要的: `vite-plugin-env-compatible`, `vite-plugin-html`, `vite`,
  * Vue2 必要的: `vite-plugin-vue2`
  * Vue3 必要的: `@vue/compiler-sfc`, `@vitejs/plugin-vue`, `@vitejs/plugin-vue-jsx`
* ✅ B02: 将 vite 入口文件 `index.html` 添加到根目录
  * 支持 `vue.config.js` 中 `pages` 选项定义的多个配置项
  * 添加像这样的入口点：`<script type="module" src="/src/main.js"></script>`，不需要添加 `dev-client` 入口点，因为 vite 默认支持 HMR
* ✅ B03: 将 vite 配置文件 `vite.config.js` 添加到根目录
* ✅ B04: 在 `vite.config.js` 中导入和使用必要的插件
  * 必要的 `vite-plugin-env-compatible`
  * Vue2 必要的: `vite-plugin-vue2`，通过 `{ jsx: true }` 选项启用 `jsx` 默认支持
  * Vue3 必要的: `@vitejs/plugin-vue`, `@vitejs/plugin-vue-jsx`
* ✅ B05: 支持省略 `.vue` 扩展名的导入
  * 在 `vite.config.js` 中，设置 `resolve.extensions` 配置项为 `['.mjs', '.js', '.ts', '.jsx', '.tsx', '.json', '.vue']`，
    然后你可能会遇到 "[Problems caused by using alisaes and omitting file suffixes at the same time](https://github.com/vitejs/vite/issues/3532)" 这样的问题，
    我们使用补丁来解决这个问题，以防万一 vite 不接受相关 PR
* ✅ B06: sass 支持
  * 如果之前使用 `node-sass` 依赖，则转换为 `sass` 依赖
* ✅ B07: postcss 8 支持
  * 如果之前使用 postcss 8，请添加 `postcss` 依赖
* ⚠️ B08: 修复问题 '[No matching export for import typescript interface](https://github.com/vitejs/vite/issues/2117)'
  * 请勿在 vite 中重复导出 typescript 类型或接口。 您只能在文件 A 中将其导出，然后在文件 B 中将其导入。不要尝试在文件 B 中将其再次导出。
    以下是重复导出类型或接口时出现的错误：
  ```
  Uncaught SyntaxError: The requested module '/src/app/reducers/state.ts' does not provide an export named 'RootState'
  ```
  * 只要删除 typescript 项目中所有重复导出的类型或接口，并修改相关导入路径
* ⚠️ B09: 删除模块热更新（或 HMR）相关代码，因为 vite 默认支持 HMR
  * 项目包含 HMR 相关代码时出现以下错误：
  ```
  index.tsx:6 Uncaught ReferenceError: module is not defined
    at index.tsx:6
  ```
* ⚠️ B10: CSS Modules
  * 在 vite 中, 任何以 `.module.css` 为后缀名的 CSS 文件都被认为是一个 CSS modules 文件
  * 这意味着你需要将以 `.css` 为后缀文件转换为以 `.module.css` 为后缀的文件来实现 CSS Modules
  * ⚠️ B11: 插件暴露的默认值
  * 当 `index.html` 包含 `htmlWebpackPlugin.options.variableName` 时出现 `htmlWebpackPlugin is not defined` 错误，需要在 `vite.config.js` 中添加插件选项：
  ```
  plugins: [
    injectHtml:({
      injectData: {
        htmlWebpackPlugin: {
          options: {
            variableName: value
          }
        }
      }
    })
  ]
  ```

### Vue-CLI 转换项
> Vue-CLI conversion are base on `vue.config.js`, map configuration to `vite.config.js`

* ✅ V01: base public path
  * `process.env.PUBLIC_URL` or `publicPath` or `baseUrl` -> `base`
* ✅ V02: css options
  * `css.loaderOptions` -> `css.preprocessorOptions`
  * `css.loaderOptions.less.lessOptions.modifyVars` -> `css.preprocessorOptions.less.modifyVars`
  * if there is only `css.loaderOptions.sass` options, convert to `css.preprocessorOptions.sass` and `css.preprocessorOptions.sass`.
    The `sass` configuration takes effect both `sass` and `scss` in Vue-CLI while vite need configure they respectively
* ✅ V03: server options
  * default set `server.strictPort = false`
  * `process.env.PORT` or `devServer.port` -> `server.port`
  * `process.env.DEV_HOST` or `devServer.public` or `devServer.host` -> `server.host`, and replace `http://` or `https://` to `''`
  * `devServer.open`, `devServer.https` -> `server.open`, `server.https`
  * `devServer.proxy` -> `server.proxy`, in proxy configuration, convert `pathRewrite` -> `rewrite`
* ✅ V04: build options
  * `outputDir` -> `build.outDir`
  * `css.extract` -> `build.cssCodeSplit`
  * if `process.env.MODERN === 'true'`, set `build.minify = esbuild`
  * if `process.env.GENERATE_SOURCEMAP === 'true'` or `vueConfig.productionSourceMap` or `css.sourceMap` -> `build.sourcemap`
* ✅ V05: `resolve.alias` options
  * add default alias options
  ```javascript
  resolve: {
    alias: [
      { find: '/^~/', replacement: ''},
      { find: '@', replacement: path.resolve(__dirname,'src') }
    ]
  }
  ```
  * convert webpack alias options to match format above
* ✅ V06: client-side env variables
  * extract variable names contained in jsp scriptlet tags
  * `VUE_APP_VARIABLE` -> `process.env['VUE_APP_VARIABLE']`
  
### Webpack 转换项
> Webpack conversion are base on `webpack.config.js` or `webpack.base.js、webpack.dev.js、webpack.prod.js|webpack.build.js|webpack.production.js`, map configuration to `vite.config.js`

> Note: if you are not using configuration file above, you need to convert configuration manually instead using tool

* ✅ W01: build input options
  * if `entry` is `string` type, `entry` -> `build.rollupOptions.input`
  * if `entry` is `object` type, convert each object property and each array element to `build.rollupOptions.input`
  * if `entry` is `function` type, convert function execute result to `build.rollupOptions.input`
* ✅ W02: outDir options
  * `output.path` -> `build.outDir`
* ✅ W03: `resolve.alias` options
  * add default alias options
  ```javascript
  resolve: {
    alias: [
      { find: '@', replacement: path.resolve(__dirname,'src') }
    ]
  }
  ```
  * convert webpack alias options to match format above
  * `resolve.alias` options key has trailing `$`, need to remove '$' and re-assign an exact path value
* ✅ W04: server options
  * `devServer.host`, `devServer.port`, `devServer.proxy`, `devServer.https`, `devServer.contentBase` -> `server.host`, `server.port`, `server.proxy`, `server.https`, `server.base`
* ✅ W05: define options
  * `new webpack.DefinePlugin()` -> `define`
  
### 其他转换项
* ⚠️ O01: use CommonJS syntax, e.g. `require('./')`
  * add vite plugin `@originjs/vite-plugin-commonjs`, see detail: https://github.com/originjs/vite-plugins/tree/main/packages/vite-plugin-commonjs
  * plugin above support part of CommonJS syntax, still, some special syntax didn't support, recommend covert to ES Modules syntax
* ❌ O02: use ElementUI, see detail: https://github.com/vitejs/vite/issues/3370
  ```
   [vite] Uncaught TypeError: Cannot read property '$isServer' of undefined
    at node_modules/_element-ui@2.15.1@element-ui/lib/utils/dom.js (:8080/node_modules/.vite/element-ui.js?v=675d2c77:1189)
    at __require (:8080/node_modules/.vite/chunk-6VNJZP5B.js?v=675d2c77:12)
    at node_modules/_element-ui@2.15.1@element-ui/lib/utils/popup/popup-manager.js (:8080/node_modules/.vite/element-ui.js?v=675d2c77:1478)
    at __require (:8080/node_modules/.vite/chunk-6VNJZP5B.js?v=675d2c77:12)
    at node_modules/_element-ui@2.15.1@element-ui/lib/utils/popup/index.js (:8080/node_modules/.vite/element-ui.js?v=675d2c77:1701)
    at __require (:8080/node_modules/.vite/chunk-6VNJZP5B.js?v=675d2c77:12)
    at node_modules/_element-ui@2.15.1@element-ui/lib/utils/vue-popper.js (:8080/node_modules/.vite/element-ui.js?v=675d2c77:2546)
    at __require (:8080/node_modules/.vite/chunk-6VNJZP5B.js?v=675d2c77:12)
    at Object.5 (:8080/node_modules/.vite/element-ui.js?v=675d2c77:6861)
    at __webpack_require__ (:8080/node_modules/.vite/element-ui.js?v=675d2c77:6547)
  ```
* ⚠️ O03: css automatic imports
  * if use `style-resources-loader` before, try to replace by `additionalData`. Example:
  ```javascript
  pluginOptions: {
    'style-resources-loader': {
      preProcessor: 'less',
      patterns: [
        resolve('src/styles/var.less'),
        resolve('src/styles/mixin.less')
      ]
    }
  }
  ```
  ->
  ```javascript
  css: {
    preprocessorOptions: {
      less: {
        additionalData: `@import 'src/styles/var.less';` + `@import 'src/styles/mixin.less';`
      }
    }
  }
  ```
* ⚠️ O04: imports path include multiple alias like: `@import '~@/styles/global.scss'`, which is includes alias `~` and `@` 
  * add an alias configure `{ find: /^~@/, replacement: path.resolve(__dirname, 'src') }` to `resolve.alias` options, and place it on first
* ⚠️ O05: use `jsx` syntax in `.vue` file
  * make sure enable `jsx` support, Vue2 add plugin `vite-plugin-vue2` and pass `{ jsx: true }` option, Vue3 add plugin `@vitejs/plugin-vue-jsx`
  * add attribute `lang="jsx"` to `script` label, e.g. `<script lang="jsx"></script>`
  * If the following error occurs
  ```
  3:54:29 PM [vite] Internal server error: /Users/Chieffo/Documents/project/Vue-mmPlayer/src/base/mm-icon/mm-icon.vue?vue&type=script&lang.tsx: Duplicate declaration "h" (This is an error on an internal node. Probably an internal error.)
  Plugin: vite-plugin-vue2
  File: /Users/Chieffo/Documents/project/Vue-mmPlayer/src/base/mm-icon/mm-icon.vue?vue&type=script&lang.tsx
      at File.buildCodeFrameError (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/core/lib/transformation/file/file.js:244:12)
      at Scope.checkBlockScopedCollisions (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/scope/index.js:421:22)
      at Scope.registerBinding (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/scope/index.js:581:16)
      at Scope.registerDeclaration (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/scope/index.js:523:14)
      at Object.BlockScoped (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/scope/index.js:240:12)
      at Object.newFn (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/visitors.js:212:17)
      at NodePath._call (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/path/context.js:53:20)
      at NodePath.call (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/path/context.js:36:14)
      at NodePath.visit (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/path/context.js:90:31)
      at TraversalContext.visitQueue (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/context.js:99:16)
      at TraversalContext.visitMultiple (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/context.js:68:17)
      at TraversalContext.visit (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/context.js:125:19)
      at Function.traverse.node (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/index.js:76:17)
      at NodePath.visit (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/path/context.js:97:18)
      at TraversalContext.visitQueue (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/context.js:99:16)
      at TraversalContext.visitSingle (/Users/Chieffo/Documents/project/Vue-mmPlayer/node_modules/@babel/traverse/lib/context.js:73:19)
  ```
  update config to `babel.config.js`
  ```javascript
  module.exports = {
    presets: [
      '@vue/app'
    ]
  }
  ```
  ->
  ```javascript
  module.exports = {
    presets: [
      ['@vue/babel-preset-jsx']
    ]
  }
  ```
  see detail: https://vuejs.org/v2/guide/render-function.html#JSX
* ⚠️ O06: use webpack api `require.context`
  * add vite plugin `@originjs/vite-plugin-require-context`, see detail: https://github.com/originjs/vite-plugins/tree/main/packages/vite-plugin-require-context
* ✅ O07: fix issue 'Compiling error when the template of the .vue file has the attribute lang="html"'
  * remove `lang="html"` attribute from `template` label, see detail: https://github.com/vuejs/vue-loader/issues/1443
* ❌ O08: use webpack api `require.ensure`
* ⚠️ O09: convert dynamic imports that paths include alias to absolute path or relative path, see detail: see detail: https://github.com/rollup/plugins/tree/master/packages/dynamic-import-vars#limitations
  ```javascript
  () => import('@/components/views/test.vue')
  ```
  ->
  ```javascript
  () => import('./components/views/test.vue')
  ```
