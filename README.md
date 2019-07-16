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

注：微信的授权操作都需要征求用户的同意，所以必须由点击事件执行此函数

实线封装：

```
//获取位置设置信息
const GetSetting = (init, refused) => {
  init = typeof (init) === 'function' ? init : function (res) { };
  refused = typeof (refused) === 'function' ? refused : function (res) { };
  wx.getSetting({
    success: (res) => {
      console.log(res);
      console.log(res.authSetting['scope.userLocation']);
      if (res.authSetting['scope.userLocation'] != undefined && res.authSetting['scope.userLocation'] != true) {
        //非初始化进入该页面,且未授权         
        wx.showModal({
          title: '是否授权当前位置',
          content: '需要获取您的地理位置，请确认授权，否则无法获取地址',
          success: function (res) {
            if (res.cancel) {
              console.info("1授权失败返回数据");
              refused();
            } else if (res.confirm) {
              wx.openSetting({
                success: function (data) {
                  console.log(data);
                  if (data.authSetting["scope.userLocation"] == true) {
                    wx.showToast({
                      title: '授权成功',
                      icon: 'success',
                      duration: 2000
                    })
                  } else {
                    wx.showToast({
                      title: '授权失败',
                      icon: 'none',
                      duration: 2000
                    });
                  }
                }
              })
            }
          }
        })
      } else if (res.authSetting['scope.userLocation'] == true) {
        init();
      } else if (res.authSetting['scope.userLocation'] == undefined) {
        //初始化进入          
        init();
      }
    }
  })
}
const GetLocation = (success, fail) => {
  success = typeof (success) === 'function' ? success : function (res) { };
  fail = typeof (fail) === 'function' ? fail : function (res) { };
  wx.getLocation({
    type: 'wgs84',
    success: success,
    fail: fail
  })
}
module.exports = { GetSetting, GetLocation }

```

调用：
```
//引入
const util = require('../../utils/util');
Page({ 
getUser:function(){
    util.GetSetting(
      () => {
        console.log("进入获取位置");
        util.GetLocation((res) => {
          //获取用户位置信息成功          
          console.log('GetLocationSuccess:', res);
          //此处获取具体位置信息this.setLocation(res.latitude, res.longitude)        
        },
          (err) => {
            //用户拒绝提供位置权限操作           
            console.log('GetLocationFial:', err);
            //wx.switchTab({ url: '../newindex/index'})       
          })
      },
      () => {
        //用户拒绝提供位置权限操作
        console.log('GetLocationFial:', err);
        // wx.switchTab({url: '../newindex/index'})

      }
    )
  }
})

```
绑定事件：
```
<view class='btn' bindtap='getUser'>
<view>
```
