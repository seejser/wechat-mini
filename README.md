# wechat-mini
wechat miniprogram
## 1.微信小程序如何让text内容空格？

```html
<text decode="{{true}}" space="{{true}}">&nbsp;&nbsp;</text>
```
## 2.微信小程序如何禁止页面上下滚动？

在json文件中添加如下代码

```
 "disableScroll": true
```
## 3.微信小程序如何获得位置？

先授权，在app.json文件中添加：

```
 "permission": {
    "scope.userLocation": {
      "desc": "你的位置信息将用于小程序位置接口的效果展示"
    }
  },
```

调用API[wx.getLocation(Object object)](https://developers.weixin.qq.com/miniprogram/dev/api/location/wx.getLocation.html)

```
wx.getLocation({
 type: 'wgs84',
 success (res) {
   const latitude = res.latitude
   const longitude = res.longitude
   const speed = res.speed
   const accuracy = res.accuracy
 }
})

```
