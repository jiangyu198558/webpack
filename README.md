一、概念: webpack 是一个现代的 JS 应用程序的模块打包器。当 webpack处理应用程序时，它会递归地构建一个依赖关系图表，其中包含应用程序需要的每个模块，然后将所有这些模块打包成少量的 bundle - 通常只有一个，由浏览器加载。

它是高度可配置的，开始前先理解四个核心概念： 入口（entry）、输出（output）、loader、插件（plugins）。

1、入口
webpack将创建所有应用程序的依赖关系图表。图表的起点被称之为入口起点。入口起点告诉webpack从哪里开始，并遵循着依赖关系图表知道要打包什么。可以将您应用程序的入口起点认为是根上下文或app第一个启动文件。

例子
module.exports = {
	entry: './path/to/my/entry/file.js'
};

单个入口语法 用法：entry: string|Array<string>
webpack.config.js
const config = {
	entry: './path/to/my/entry/file.js'
};
module.exports = config;

或者简写
const config = {
	entry: {
		main: './path/to/my/entry/file.js'
	}
};

对象语法 用法：entry: {[entryChunkName: string]: string|Array<string>}
webapack.config.js
const config = {
	entry: {
		app: './src/app.js',
		vendors: './src/vendors.js'
	}
};

常见场景

分离 应用程序app和公共库vendor入口
const config = {
	entry: {
		app: './src/app.js',
		vendors: './src/vendor.js'
	}
};
module.exports = config;

多个页面应用程序
webpack.config.js
const config = {
	entry: {
		pageOne: './src/pageOne/index.js',
		pageTwo: './src/pageTwo/index.js',
		pageThree: './src/pageThree/index.js'
	}
};

2、输出[Output]
此选项影响 compilation 对象的输出。output 选项控制 webpack 如何向硬盘写入编译文件。注意，即使可以存在多个入口起点，但只指定一个输出配置。

用法[Usage]
在webpack中配置output属性的最低要求是，将它的值设置为一个对象，包括以下两点：
编译文件的文件名(filename)，首选推荐: //main.js || bundle.js || index.js
output.path对应一个绝对路径，此路径是你希望一次性打包的目录。

webpack.config.js
const config = {
	output: {
		filename: 'bundle.js',
		path: '/home/proj/public/assets'
	}
};
module.exports = config;

选项[Options]
output.chunkFilename
非入口的chunk(non-entry chunk)的文件名，路径相对于output.path目录
[id] 被chunk的id替换
[name] 被chunk的name替换
[hash] 被compilation生命周期的hash替换
[chunkhash] 被chunk的hash替换

output.crossOriginLoading
此选项启用跨域加载(cross-origin loading) chunk。
可选的值有：
false - 禁用跨域加载
"anonymous" - 启用跨域加载。当使用 anonymous 时，发送不带凭据(credential)的请求。
"use-credentials" - 启用跨域加载。发送带凭据(credential)的请求。

output.devtoolLineToLine
所有/指定模块启用行到行映射(line-to-line mapped)模式。行到行映射模式使用一个简单的 SourceMap，即生成资源(generated source)的每一行都映射到原始资源(original source)的同一行。这是一个可做性能优化之处。当你需要更好的性能，并且只要确保输入行(input line)和生成行(generated line)匹配时，才会考虑启用。

output.filename
指定硬盘每个输出文件的名称。在这里你不能指定为绝对路径！output.path 选项规定了文件被写入硬盘的位置。filename 仅用于命名每个文件。

单个文件入口
{
	entry: './src/app.js',
	output: {
		filename: 'bundle.js',
		path: __dirname + '/build'
	}
}

多个入口
如果你的配置创建了多个 "chunk"（例如使用多个入口起点或使用类似 CommonsChunkPlugin 的插件），你应该使用以下的替换方式来确保每个文件名都不重复。
[name] 被 chunk 的 name 替换。
[hash] 被 compilation 生命周期的 hash 替换。
[chunkhash] 被 chunk 的 hash 替换。

{
	entry: {
		app: './src/app.js',
		search: './src/search.js'
	},
	output: {
		filename: '[name].js',
		path: __dirname + '/build'
	}
}

output.hotUpdateChunkFilename
热更新 chunk(Hot Update Chunk) 的文件名。它们在 output.path 目录中。
[id] 被 chunk 的 id 替换。
[hash] 被 compilation 生命周期的 hash 替换。（最后一个 hash 存储在记录中）
默认值："[id].[hash].hot-update.js"

output.hotUpdateMainFilename
热更新主文件(hot update main file)的文件名
[hash] 被compilation生命周期的hash替换


output.jsonpFunction
webpack 中用于异步加载(async loading) chunk 的 JSONP 函数。
较短的函数可能会减少文件大小。当单页有多个 webpack 实例时，请使用不同的标识符(identifier)。

output.library
如果设置此选项，会将 bundle 导出为 library。output.library 是 library 的名称。

output.libraryTarget
library 的导出格式
"var" - 导出为一个变量：var Library = xxx（默认）
"this" - 导出为 this 的一个属性：this["Library"] = xxx
"commonjs" - 导出为 exports 的一个属性：exports["Library"] = xxx
"commonjs2" - 通过 module.exports：module.exports = xxx 导出
"amd" - 导出为 AMD（可选命名 - 通过 library 选项设置名称）
"umd" - 导出为 AMD，CommonJS2 或者导出为 root 的属性

output.path
导出目录为绝对路径（必选项）。
[hash] 被compilation生命周期的hash替换。

config.js
output: {
	path: '/home/proj/public/assets',
	publicPath: '/assets/'
}

index.html
<head>
	<link href="/assets/spinner.gig"/>
</head>

config.js
output: {
	path: '/home/proj/cdn/assets/[hash]',
	publicPath: 'http://cdn.example.com/assets/[hash]/'
}

output.sourceMapFilename
JavaScript 文件的 SourceMap 的文件名。它们在 output.path 目录中。
[file] 被 JavaScript 文件的文件名替换。
[id] 被 chunk 的 id 替换。
[hash] 被 compilation 生命周期的 hash 替换。

3、Loader
loader用于对模块的源代码进行转换。loader可以使你在require()或加载模块时预处理文件。loader类似于其他构建工具中“任务（task）”，并提供了处理前端构建步骤的强大方法。loader 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript，或将内联图像转换为 data URL。loader 甚至允许你在 JavaScript 中 require（） CSS文件！

示例
你可以使用 loader 告诉 webpack 加载 CSS 文件，或者将 TypeScript 转为 JavaScript。首先，安装相对应的 loader：
npm install --save-dev css-loader
npm install --save-dev ts-loader

配置webpack.config.js，对每个.css文件使用css-loader,同样对每个.ts文件使用ts-loader
webpack.config.js
module.exports = {
	module: {
		rules: [
			{test: /\.css$/, use: 'css-loader'},
			{test: /\.ts$/, use: 'ts-loader'}
		]
	}
}

配置
三种方式使用loader
通过配置
在require语句中显示使用
通过CLI

通过webpack.config.js
module.rules允许你在webpack配置中指定几个loader

module: {
	rules: [
		{
			test: /\.css$/,
			use: [
				{loader: 'style-loader'},
				{
					loader: 'css-loader',
					options: {
						modules: true
					}
				}
			]
		}
	]
}

通过require
可以在 require 语句（或 define, require.ensure, 等语句）中指定 loader。使用 ! 将资源中的 loader 分开。分开的每个部分都相对于当前目录解析。
require('style-loader!css-loader?modules!./styles.css');

通过 CLI
webpack --module-bind jade-loader --module-bind 'css=style-loader!css-loader'

Loader 特性
loader 支持链式传递。能够对资源使用流水线(pipeline)。loader 链式地按照先后顺序进行编译。loader 链中的第一个 loader 返回值给下一个 loader。在最后一个 loader，返回 webpack 所预期的 JavaScript。
loader 可以是同步或异步函数。
loader 运行在 Node.js 中，并且能够执行任何可能的操作。
loader 接收查询参数。用于 loader 间传递配置。
loader 也能够使用 options 对象进行配置。
除了使用 package.json 常见的 main 属性，还可以将普通的 npm 模块导出为 loader，做法是在 package.json 里定义一个 loader 字段。
插件(plugin)可以为 loader 带来更多特性。
loader 能够产生额外的任意文件。
loader 通过（loader）预处理函数，为 JavaScript 生态系统提供了更多有力功能。用户现在可以更加灵活的引入细粒度逻辑，例如压缩(compression)、打包(package)、语言翻译(language translation)和其他更多。

解析 Loader
loader 遵循标准的模块解析。多数情况下，loader 将从模块路径（通常将模块路径认为是 npm install, node_modules）解析。
如何编写模块？loader 模块需要导出为一个函数，并且使用 Node.js 兼容的 JavaScript 编写。在通常情况下，你会使用 npm 来管理 loader，但是你也可以将 loader 模块作为应用程序中的文件。
按照约定，loader 通常被命名为 XXX-loader，其中 XXX 是上下文的名称，例如 json-loader。
loader 的名称约定和优先搜索顺序，由 webpack 配置 API 中的 resolveLoader.moduleTemplates 定义

4、插件(Plugins)
webpack 插件是一个具有 apply 属性的 JavaScript 对象。 apply 属性会被 webpack compiler 调用，并且 compiler 对象可在整个 compilation 生命周期访问。

ConsoleLogOnBuildWebpackPlugin.js

function ConsoleLogOnBuildWebpackPlugin(){
	
}

ConsoleLogOnBuildWebpackPlugin.prototype.apply = function(compiler){
	compiler.plugin('run', function(compiler, callback){
		console.log("webpack 构建过程开始!!!");
		callback();
	});
}

用法
由于 plugin 可以携带参数/选项，你必须在 wepback 配置中，向 plugins 属性传入 new 实例。
根据你如何使用 webpack，这里有多种方式使用插件。

配置
webpack.config.js

const HtmlWebpackPlugin = require('html-webpack-plugin');
const webpack = require('webpack');
const path = require('path');

const config = {
	entry: './path/to/my/entry/file.js',
	output: {
		filename: 'my-first-webpack.bundle.js',
		path: path.resolve(__dirname, 'dist')
	},
	module: {
		loaders: [
			{
				test: /\.(js|jsx)$/,
				loader: 'babel-loader'
			}
		]
	},
	plugins: [
		new webpack.optimize.UglifyJsPlugin(),
		new HtmlWebpackPlugin({template: './src/index.html'})
	]
};

module.exports = config;

Node API
some-node-script.js
const webpack = require('webpack'); //运行时(runtime)访问 webpack
const configuration = require('./webpack.config.js');

let compiler = webpack(configuration);
compiler.apply(new webpack.ProgressPlugin());

compiler.run(function(err, stats) {
  // ...
});
