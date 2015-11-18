# webpack的好处

- 与browserify相似, 而且还能将应用分成多个文件, 如果在单页面中包含多个页面, webpack只需要加载当前页面的代码, 且代码切换时不会重新加载公用代码;
- 可以替代grunt或gulp

# 怎样使用webpack

- webpack: 只执行一次
- webpack -p: 只为生产环境执行一次
- webpack --watch: 监控文件变化并在变化后执行
- webpack -d: 包含资源map文件

# JS编译

- 使用webpack的loaders: coffee-loader、babel-loader(Example)
```
module: {
   loaders: [
      { test: /\.coffee$/, loader: 'coffee-loader' },
      { test: /\.js$/, loader: 'babel-loader' }
   ]
}
```
- 不使用后缀名需要添加resolve.extensions
```
resolve: {
   // you can now require('file') instead of require('file.coffee')
   extensions: ['', '.js', '.json', '.coffee']
}
```

#Stylesheets and images
```
module.exports = {
   entry: './main.js',
   output: {
      path: './build', // This is where images AND js will go
      publicPath: 'http://mycdn.com/', // This is used to generate URLs to e.g. images
      filename: 'bundle.js'
   },
   module: {
      loaders: [
         { test: /\.less$/, loader: 'style-loader!css-loader!less-loader' }, // use ! to chain loaders
         { test: /\.css$/, loader: 'style-loader!css-loader' },
         {test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'} // inline base64 URLs for <=8k images, direct URLs for the rest
      ]
   }
};
```

#Feature flags
```
// definePlugin takes raw strings and inserts them, so you can put strings of JS if you want.
var definePlugin = new webpack.DefinePlugin({
   __DEV__: JSON.stringify(JSON.parse(process.env.BUILD_DEV || 'true')),
   __PRERELEASE__: JSON.stringify(JSON.parse(process.env.BUILD_PRERELEASE || 'false'))
});

module.exports = {
   entry: './main.js',
   output: {
      filename: 'bundle.js'       
   },
   plugins: [definePlugin]
};
```


#Multiple entrypoints

每个页面可以通过入口加载入口所对应的文件, 而不需要全部加载

```
module.exports = {
   entry: {
      Profile: './profile.js',
      Feed: './feed.js'
   },
   output: {
      path: 'build',
      filename: '[name].js' // Template based on keys in entry above
   }
};
```

#优化公共代码

不同的页面可能有公用代码, webpack可以将公共代码打包并缓存

```
var webpack = require('webpack'),
      commonsPlugin = new webpack.optimize.CommonsChunkPlugin('common.js');

module.exports = {
   entry: {
      Profile: './profile.js',
      Feed: './feed.js'
   },
   output: {
      path: 'build',
      filename: '[name].js' // Template based on keys in entry above
   },
   plugins: [commonsPlugin]
};
```

然后在页面中加入```<script src="build/common.js"></script>```引入公用代码

#异步加载

CommonJS是同步加载的, 而webpack提供了一种异步加载的方法.
