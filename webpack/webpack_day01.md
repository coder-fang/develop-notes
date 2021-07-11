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