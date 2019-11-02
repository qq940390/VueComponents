# SvgIcon

移动端适用的用于加载 \*.svg 格式文件的组件

## 使用方法

1. 复制 SvgIcon 文件夹到你的项目存放组件的位置
2. webpack 配置

    ```javascript
    module: {
        rules: [
            {
                test: /\.svg$/,
                loader: 'svg-sprite-loader',
                include: [resolve('src/icons/svg')],
                options: {
                    symbolId: 'icon-[name]'
                }
            },
            {
                test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
                loader: 'url-loader',
                exclude: [resolve('src/icons/svg')],
                options: {
                    limit: 10000,
                    name: 'static/images/[name].[hash:7].[ext]'
                }
            }
        ]
    }
    ```

    src/icons/svg 文件夹用于存放 svg 文件

3. 增加 src/icons/index.js 文件，内容为

    ```javascript
    import Vue from 'vue'
    import SvgIcon from '@components/SvgIcon' // svg component

    // register globally
    Vue.component('svg-icon', SvgIcon)

    const req = require.context('./svg', false, /\.svg$/)
    const requireAll = requireContext => requireContext.keys().map(requireContext)
    requireAll(req)
    ```

4. 在入口 js 中导入

    ```javascript
    import './icons'
    ```

5. 在需要的地方使用，比如某个 vue 文件里
    ```html
    <svg-icon icon-class="avatar"></svg-icon>
    ```
    avatar 就是 svg 的文件名
