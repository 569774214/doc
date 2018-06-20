```
const path = require('path');
const webpack = require('webpack');
// 用于清空 dist 目录。
const CleanWebpackPlugin = require('clean-webpack-plugin');
// 用于把src的文件复制到dist
const CopyWebpackPlugin = require('copy-webpack-plugin');
// 用于把最终的 css 分离成单独文件
const ExtractTextPlugin = require('extract-text-webpack-plugin');
/* 生成html */
var HtmlWebpackPlugin = require('html-webpack-plugin');


module.exports = {

    // 入口文件的配置项，可以指定多个入口起点
    entry: {

        'index': './src/jq/index.js',
        'preview': './src/jq/preview.js',

    },

    output: {
        // path：指用来存放打包后文件的输出目录
        path: path.resolve(__dirname,'dist'),
        // publicPath：指定资源文件引用的目录
        filename: '[name].js'
    },

    externals: {
        $: 'jquery',
        jQuery: 'jquery',
        'window.jQuery': 'jquery',
        'window.$': 'jquery',
    },

    // 模块：例如解读CSS,图片如何转换，压缩
    module: {
        rules: [ // 用于规定在不同模块被创建时如何处理模块的规则数组

            {
                test: /\.js$/,
                exclude: /node_modules/,
                loader: "babel-loader"
            },
            //添加对样式表.css格式文件的处理
            {
                test: /\.css$/,
                // use: [
                //     {loader: 'style-loader'},
                //     {loader: 'css-loader'},
                //     {loader: 'postcss-loader'}
                // ]
                // 使用 'style-loader','css-loader'
                use:ExtractTextPlugin.extract({
                    fallback:'style-loader', // 回滚
                    use:'css-loader'
                })
            },
            {
                test: /\.art$/,
                loader: "art-template-loader",
                options: {
                    // art-template options (if necessary)
                    // @see https://github.com/aui/art-template
                }
            },
            {
                test:/\.(png|jpg|gif)$/,
                use:[{
                    loader:'url-loader',
                    options:{ // 这里的options选项参数可以定义多大的图片转换为base64
                        limit:50000, // 表示小于50kb的图片转为base64,大于50kb的是路径
                        outputPath:'assets/images', //定义输出的图片文件夹
                        name:'[name].[ext]',
                    }
                }]
            }
        ]
    },

    // 插件，用于生产模版和各项功能
    plugins: [

        // 清空dist目录，第一个参数是要清理的目录的字符串数组
        new CleanWebpackPlugin(['./dist']),

        /* 复制文件，把src的css、images文件复制到pc下  */
        new CopyWebpackPlugin([
          {from:path.resolve(__dirname,'./src/assets'),to:path.resolve(__dirname,'./dist/assets')},
        ]),

        new ExtractTextPlugin('assets/css/[name].css'),

        new HtmlWebpackPlugin({
            title: 'title',
            filename: 'index.html',

            template: './src/jq/index.html',
            inject: 'body',
            hash: true,
            chunks: ['index'],
            minify: {
                removeComments: true, // 移除HTML中的注释
                collapseWhitespace: true // 删除空白符与换行符
            }
        }),
        new HtmlWebpackPlugin({
            title: 'preview',
            filename: 'preview.html',

            template: './src/jq/preview.html',
            inject: 'body',
            hash: true,
            chunks: ['preview'],
            minify: {
                removeComments: true, // 移除HTML中的注释
                collapseWhitespace: true // 删除空白符与换行符
            }
        }),
    ],

    devServer: {
        contentBase: path.resolve(__dirname, './dist'),
        host: 'localhost',
        disableHostCheck: true, //绕过主机检查
        hot: true,
        https: false,  // 是否采用https，默认是http
        inline: true,
        progress: true, // 输出运行进度到控制台。
        watchContentBase: true, //观察contentBase选项提供的文件。文件更改将触发整页重新加载
        compress: true,
        port: 8000
    }
};

```
