# Blog

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

---

### web端hover事件在mobile端的处理

*其实mobile端的touch事件可以触发web的hover效果，我们需要做的其实就是给元素加上一个touch事件就可以了，但是在Safari上，可能有时候会出现点击其他区域，无法消除hover效果的情况出现。*

```
document.body.addEventListener('touchstart', () => {});
```

---

### request的封装

可以使用Proxy对wx.request进行一层封装,其他网络请求同理

```
export default () => {
  let handler = {
    get (target, property) {
        target[property] = (url, params = {}) => {
          return new Promise((resolve, reject) => {
            $wx.request({
              url,
              method: property.toLocaleUpperCase(),
              ...params,
              success: res => {
                resolve(res.data)
              },
              fail: error => {
                reject(error)
              }
            })
          })
        }
        return target[property]
    }
  }

  const API = new Proxy({}, handler)
  return API
}
```

使用

```
  API.get(url)
    .then(res => {
      // handle with response
    })
    .catch(err => {
      // handle with error
    })

  API.post(url)
    .then(res => {
      // handle with response
    })
    .catch(err => {
      // handle with error
    })
```