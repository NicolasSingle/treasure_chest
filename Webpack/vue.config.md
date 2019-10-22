### vue-cli 3.x vue.config.js参考配置
> **chainWebpack**相关语法见[官方配置](http://github.com/neutrinojs/webpack-chain) 
* chainWebpack和configureWebpack的区别?
    >两者都可以直接修改webpack的config,有对象和函数两种,  但一般configureWebpack用来区分生产和开发环境时修改config, chainWebpack用来修改webpack内部配置, 如配置loader 增加loader 修改plugin 修改resolve
    > 具体还得再观摩...

```
const UglifyJsPlugin = require('uglifyjs-webpack-plugin')
const path = require('path')
const webpack = require('webpack')
let externals = {}
let IsProd = process.env.NODE_ENV == 'production'
let IsForTest = process.env.VUE_APP_FOR_TEST
function resolve(dir) {
    return path.join(__dirname, dir)
}
if (IsProd) {
    externals = {
        vue: 'Vue',
        axios: 'axios',
        'vue-router': 'VueRouter',
        'element-ui': 'ELEMENT',
        jquery: '$',
        echarts: 'echarts',
        'v-charts': 'VeIndex'
    }
}

module.exports = {
    baseUrl: IsProd ? (IsForTest ? '/' : '/') : './',
    pages: {
        index: {
            entry: 'src/main.js',
            template: 'public/index.html',
            filename: 'index.html',
            commonText: `
                <script src="/other/jquery.min.3.3.1.js"></script>
            `,
            propText: IsProd
                ? `    
                <script src="/other/vue.min.2.5.22.js"></script>
                <script src="/other/vue-router.min.3.0.1.js"></script>
                <script src="/other/element-ui.2.4.11.js"></script>
                <script src="/other/axios.0.18.0.min.js"></script>
                <script src="/other/echarts.min.js"></script>
                <script src="/other/vcharts.min.js"></script>
            `
                : ''
        }
    },
    productionSourceMap: IsProd,
    configureWebpack: config => {
        if (!IsProd) {
            config.devtool = 'inline-source-map'
        } else {
            config.devtool = ''
            config.plugins.push(
                new UglifyJsPlugin({
                    uglifyOptions: {
                        warnings: false,
                        compress: {
                            drop_console: true,
                            pure_funcs: ['console.log'] // 移除console
                        }
                    },
                    sourceMap: false,
                    parallel: true
                })
            )
        }
    },
    chainWebpack: config => {
        const oneOfsMap = config.module.rule('less').oneOfs.store
        oneOfsMap.forEach(item => {
            item.use('sass-resources-loader')
                .loader('sass-resources-loader')
                .options({
                    resources: [
                        './src/assets/less/utils/function.less',
                        './src/assets/less/utils/variable.less'
                    ]
                })
                .end()
        })
        config.resolve.alias
            .set('@', resolve('src'))
            .set('assets', resolve('src/assets'))
            .set('components', resolve('src/components'))
            .set('views', resolve('src/views'))
            .set('store', resolve('src/store'))
            .set('router', resolve('src/router'))
            .set('config', resolve('src/config'))
            .set('directives', resolve('src/directives'))
            .set('utils', resolve('src/utils'))
            .set('api', resolve('src/api'))
            .set('mixin', resolve('src/mixin'))
        config.resolve.extensions
            .prepend('.css')
            .prepend('.js')
            .prepend('.vue')
        config.externals(externals)
        config.plugin('provide').use(webpack.ProvidePlugin, [
            {
                Vue: 'vue/dist/vue.js'
            }
        ])
    },
    devServer: {
        open: true,
        host: '0.0.0.0',
        port: 8800,
        proxy: {
            '/': {
                target: 'http://www.xxx.com/',
                // 用于解决 websocket 报错问题
                ws: false,
                secure: false
            }
        }
    }
}

```