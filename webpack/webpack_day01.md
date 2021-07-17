第一章： webpack简介

01.webpack是什么？
				Webpack是一种前端资源构建工具，一个静态模块打包器(module bundler)。
				在Webpack看来，前端的所有资源文件(js/json/css/img/less/..)都会作为模块(chunk)处理。
				它将根据模块的依赖关系进行静态分析，打包生成对应的静态资源(bundle)。

1.2 webpack的五个核心概念

1.2.1 Entry
			入口(Entry)指示Webpack以哪个文件为入口起点开始打包，分析构建内部依赖图。
		1.2.2 Output
			输出(Output)指示 Webpack打包后的资源bundles输出到哪里去，以及如何命名。
		1.2.3 Loader
			Loader 让 Webpack能够去处理那些非JavaScript文件(webpack自身只理解JavaScript)

1.2.4 Plugins
			插件(Plugins)可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩一直到重新定义环境中的变量等。

![image-20210711090045183](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210711090045183.png)

02.项目初体验

![image-20210711091715765](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210711091715765.png)

![image-20210711091737166](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210711091737166.png)

![image-20210711093955412](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210711093955412.png)

![image-20210711100351755](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210711100351755.png)

在node环境就可以运行：

![image-20210711101115485](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210711101115485.png)

![image-20210711101907726](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210711101907726.png)

![image-20210711101618523](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210711101618523.png)

说明引入打包后的资源都能够正常运行。

* 验证json是否可以被webpack解析
  1. 在src下新建data.json文件

![image-20210711102333727](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210711102333727.png)

			2. 在src下的index.js入口文件中引入data.json

3. 修改完index.js后要重新打包

<code>webpack ./src/index.js -o ./build/build.js --mode=development</code>

打包后运行，看浏览器console打印结果：

![image-20210711103648261](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210711103648261.png)

  /**

 \* index.js : webpack入口起点文件

 \* 

 \* 

 \* 1. 运行指令:

 \* 开发环境: webpack ./src/index.js -o ./build/build.js --mode=development

 \*   webpack 会以./src/index.js 为入口文件开始打包，打包后输出到 ./build/build.js

 \*   整体打包环境，是开发环境。

 \* 生产环境：webpack ./src/index.js -o ./build/build.js --mode=production

 \*   webpack 会以./src/index.js 为入口文件开始打包，打包后输出到 ./build/build.js

 \*   整体打包环境，是生产环境。(会压缩js代码)

 \* 2. 结论：

 \* 2.1 webpack可以处理js/json,不能处理css/img等其他资源

 \* 2.2 生产环境和开发环境将ES6模块化编译成浏览器能识别的模块化

 \* 2.3 生产环境比开发环境多一个压缩js代码 

 */

03.打包样式资源 

08.开发环境webpack基本配置

09.生产环境下，提取css成单独文件

![image-20210714113326012](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210714113326012.png)

打包后会发现css样式还是在js文件中,要提取css，就要下载一个插件:<code>npm i mini-css-extract-plugin -D</code> 可以选择全局下载到项目中。

在配置文件 webpack.config.js 中引入并使用这个插件（使用了2次）：

​	①new 使用一次

​	②取代style-loader 使用一次



```
const { resolve } = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
module.exports = {
    entry: './src/js/index.js',
    output: {
        filename: 'built.js',
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: [{
                //匹配哪些文件
                test: /\.css$/,
                // 使用哪些loader进行处理
                use: [
                    //use数组中loader执行顺序: 从右到左，从下到上，依次执行
                    //在页面中打开时，创建style标签，将js中的样式资源插入进去，添加到head中生效
                    // 'style-loader',
                    //MiniCssExtractPlugin  取代css-loader 作用：提取js中的css成单独文件    
                    MiniCssExtractPlugin.loader,
                    //将css文件变成commonjs模块加载到js中，里面内容是样式字符串
                    'css-loader'
                ]
            },
            {
                test: /\.less$/,
                use: [
                    'style-loader',
                    'css-loader',
                    //将less文件编译成css文件   
                    //需要下载less-loader和less
                    'less-loader'
                ]
            },
            // 打包其他资源，除了html、js、css资源以外的资源
            {
                //排除html、js、css资源
                exclude: /\.html|js|css|png|jpg|svg$/,
                loader: 'file-loader',
                //配置
                options: {
                    name: '[hash:10].[ext]',
                    outputPath: 'media'
                }
            },
            {
                //处理图片资源
                test: /\.(jpg|png|gif|svg)$/,
                loader: 'url-loader',
                options: {
                    //图片大小小于8kb，就会被base64处理
                    //优点：减少请求数量（减轻服务器压力）
                    //缺点：图片体积会更大（文件请求速度更慢）
                    limit: 8 * 1024,
                    //[hash:10] 取图片的hash的前10位   [ext]取文件原来的扩展名

                    esModule: false,
                    outputPath: 'imgs',

                    name: '[hash:10].[ext]'

                }
            },
            {
                test: /\.html$/,
                //处理html文件的img图片（负责引入img，从而能被url-loader进行处理）
                loader: 'html-withimg-loader',
                options: {
                    esModule: false,
                }
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html'
        }),
        new MiniCssExtractPlugin({
            //对输出的css文件进行重命名
            filename: 'css/built.css'
        })
    ],
    mode: 'development',
    //开发服务器 DevServer: 用来自动化（自动编译，自动打开浏览器，自动刷新浏览器）
    //特点: 只会在内存中编译打包，不会有任何输出(一旦终止运行，它会把内存中的东西删掉)
    //启动devServer 指令为: npx webpack server 或npx webpack s
    devServer: {
        //项目构建后路径
        contentBase: resolve(__dirname, 'build'),
        //启动gzip 压缩
        compress: true,
        //端口号
        port: 3000,
        //自动打开浏览器
        open: true
    }
}
```

之后删掉build文件夹后，运行：webpack或npx webpack s(server)

浏览器展示效果: ![image-20210714140804294](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210714140804294.png)

表明： 1. 样式以link标签引入，而不是以style标签引入，可避免出现闪屏现象。

​			2.并且css和js文件分隔开了，因此js文件没有这么大，解析速度比较快。

10.css兼容性处理

下载npm i postcss-loader  postcss-preset-env -D

```
const { resolve } = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
    //设置nodejs环境变量
process.env.NODE_ENV = "development"
module.exports = {
    entry: './src/js/index.js',
    output: {
        filename: 'built.js',
        path: resolve(__dirname, 'build')
    },
    module: {
        rules: [{
                //匹配哪些文件
                test: /\.css$/,
                // 使用哪些loader进行处理
                use: [

                    MiniCssExtractPlugin.loader,
                    'css-loader',
                    /**
                     * css兼容性处理: postcss库 ----> postcss-loader  post-prest-env
                     *  插件作用： 帮postcss找到package.json中browserslist里的配置，通过配置加载指定的css兼容性样式
                                *    "browerslist": {
                                * //开发环境： 需设置node环境变量： process.env.NODE_ENV = "development"
                                        "development": [
                                            "last 1 chrome version",
                                            "last 1 firefox version",
                                            "last 1 safari version"
                                        ],
                                        //生产环境： 默认是生产环境
                                        "production": [
                                            ">0.2%",
                                            "not dead",
                                            "not op_mini all "
                                        ]
                                    }
                     */
                    //使用loader的默认配置
                    // 'postcss-loader',
                    //修改loader配置
                    {
                        loader: "postcss-loader",
                        options: {
                            postcssOptions: {
                                plugins: [
                                    require("postcss-preset-env")(),
                                ]
                            }
                        }
                    }
                ]
            },
            {
                test: /\.less$/,
                use: [
                    'style-loader',
                    'css-loader',
                    //将less文件编译成css文件   
                    //需要下载less-loader和less
                    'less-loader'
                ]
            },
            // 打包其他资源，除了html、js、css资源以外的资源
            {
                //排除html、js、css资源
                exclude: /\.html|js|css|png|jpg|svg$/,
                loader: 'file-loader',
                //配置
                options: {
                    name: '[hash:10].[ext]',
                    outputPath: 'media'
                }
            },
            {
                //处理图片资源
                test: /\.(jpg|png|gif|svg)$/,
                loader: 'url-loader',
                options: {
                    //图片大小小于8kb，就会被base64处理
                    //优点：减少请求数量（减轻服务器压力）
                    //缺点：图片体积会更大（文件请求速度更慢）
                    limit: 8 * 1024,
                    //[hash:10] 取图片的hash的前10位   [ext]取文件原来的扩展名

                    esModule: false,
                    outputPath: 'imgs',

                    name: '[hash:10].[ext]'

                }
            },
            {
                test: /\.html$/,
                //处理html文件的img图片（负责引入img，从而能被url-loader进行处理）
                loader: 'html-withimg-loader',
                options: {
                    esModule: false,
                }
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html'
        }),
        new MiniCssExtractPlugin({
            //对输出的css文件进行重命名
            filename: 'css/built.css'
        })
    ],
    mode: 'development',
    //开发服务器 DevServer: 用来自动化（自动编译，自动打开浏览器，自动刷新浏览器）
    //特点: 只会在内存中编译打包，不会有任何输出(一旦终止运行，它会把内存中的东西删掉)
    //启动devServer 指令为: npx webpack server 或npx webpack s
    devServer: {
        //项目构建后路径
        contentBase: resolve(__dirname, 'build'),
        //启动gzip 压缩
        compress: true,
        //端口号
        port: 3000,
        //自动打开浏览器
        open: true
    }
}
```

11. 压缩css

下载插件 npm i optimize-css-assets-webpack-plugin -D

12. js兼容性处理

正常来讲，一个文件只能被一个loader处理。

当一个文件要被多个loader处理，那么一定要指定loader执行的先后顺序。

先执行eslint（js语法检查），再执行babel（js兼容性处理）

![image-20210714164425051](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210714164425051.png![image-20210714164504936](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210714164504936.png)

![image-20210714164630859](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210714164630859.png)

13. mode为生产环境时，会自动压缩js和json代码

14. html压缩

![image-20210714155620904](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210714155620904.png![image-20210714155720328](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210714155720328.png)

15. 优化配置

webpack性能优化

 1. 开发环境性能优化

    优化打包构建速度

    优化代码调试

 2. 生产环境性能优化

    ​	优化打包构建速度

    ​	优化代码运行的性能

    16.
