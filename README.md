# 记录一下项目中遇到的坑

## Vue

### Webpack -webkit-box-orient: vertical打包放到线上不显示

项目中使用了 -webkit-line-clamp 属性来进行文字的单行省略号处理，访问调试服务器的时候样式正常，但打完包之后，样式就失效了。

查了很久，最后发现在webpack打包后，css文件里并没有这个属性，导致了最后样式的不生效。

解决方案有两种：

* 在属性前后加上注释

```CSS
/*! autoprefixer: off */
-webkit-box-orient: vertical;
/* autoprefixer: on */
```

* 修改webpack的配置

在webpack.config.js文件中，找到 new webpack.optimize.UglifyJsPlugin，在里面添加一条 mangle:false，这个的意思是删除注释。这样打包时就注释和这行代码就不会连在一起了。