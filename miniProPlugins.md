# 小程序第三方插件

1. 安装: npm i wx-js-utils

2. 生成二维码

```
// 云函数入口文件
const cloud = require('wx-server-sdk')

cloud.init()

const {
  WXMINIUser,
  WXMINIQR
} = require('wx-js-utils');

const appId = 'wxeab001f057572370'; // 小程序 appId
const secret = 'e894f71a4cd548277ad0b5e0d590588d'; // 小程序 secret

// 云函数入口函数
exports.main = async (event, context) => {

  let wXMINIUser = new WXMINIUser({
    appId,
    secret
  });

  // 一般需要先获取 access_token
  let access_token = await wXMINIUser.getAccessToken();
  let wXMINIQR = new WXMINIQR();

  // 获取小程序二维码
  let qrResult = await wXMINIQR.getQR({
    access_token,
    path: 'pages/serverRequest/serverRequest'
  });

  return await cloud.uploadFile({
    cloudPath:"normalQr.png",
    fileContent:qrResult
  })
}
```

3. 生成小程序码

```
// 云函数入口文件
const cloud = require('wx-server-sdk')

cloud.init()

const {
  WXMINIUser,
  WXMINIQR
} = require('wx-js-utils');

const appId = 'wxeab001f057572370'; // 小程序 appId
const secret = 'e894f71a4cd548277ad0b5e0d590588d'; // 小程序 secret

// 云函数入口函数
exports.main = async (event, context) => {
  let wXMINIUser = new WXMINIUser({
    appId,
    secret
  });

  // 一般需要先获取 access_token
  let access_token = await wXMINIUser.getAccessToken();
  let wXMINIQR = new WXMINIQR();

  // 获取小程序码
  let qrResult = await wXMINIQR.getMiniQRLimit({
    access_token,
    path: 'pages/getMiniQr/getMiniQr',
    is_hyaline: true
  });

  return await cloud.uploadFile({
    cloudPath:"qr.png",
    fileContent:qrResult
  })
}
```

4. 发送模板消息


```
// 发送模板消息需要form_id,前台需要进行相应设置

<form report-submit="{{true}}" bindsubmit="onSubmit">
  <button  form-type="submit">推送模板消息</button>
</form>



// js调用云函数，将form_id传递给云函数

onSubmit(e){
    wx.cloud.callFunction({
      name:"templateMessage",
      data:{
        formId:e.detail.formId
      }
    }).then(console.log)
 }
```

```
// 云函数入口文件
const cloud = require('wx-server-sdk')

cloud.init()

const {
  WXMINIUser,
  WXMINIMessage
} = require('wx-js-utils');

const appId = 'wxeab001f057572370'; // 小程序 appId
const secret = 'e894f71a4cd548277ad0b5e0d590588d'; // 小程序 secret

// 云函数入口函数
exports.main = async (event, context) => {

  const { OPENID } = cloud.getWXContext();

  // 获取 access_token
  let wXMINIUser = new WXMINIUser({
    appId,
    secret
  });

  let access_token = await wXMINIUser.getAccessToken();

  const touser = OPENID; // 小程序用户 openId，从用户端传过来，指明发送消息的用户
  const form_id = event.formId; // 小程序表单的 form_id，或者是小程序微信支付的 prepay_id
  const template_id = 'xz3NseEM7j7tXnARI5mG_yT_q3h14DwyGTDP_L-t6lw'; // 小程序模板消息模板 id ，在个人账户中设置

  // 发送模板消息
  let wXMINIMessage = new WXMINIMessage();

  let result = await wXMINIMessage.sendMessage({
    access_token,
    touser,
    form_id,
    template_id,
    data: {
      keyword1: {
        value: '18342228045' // keyword1 的值
      },
      keyword2: {
        value: '120' // keyword2 的值
      },
      keyword3: {
        value: '2019-09-20' // keyword2 的值
      },
      keyword4: {
        value: '苹果' // keyword2 的值
      }
    },
    page: 'pages/templateMessage/templateMessage' // 点击模板消息后，跳转的页面
  })

  return result;
}
```

# 小程序其他功能

1. 上传图片

```
// wxml 代码

<button bindtap="chooseImgae">上传图片</button>
<block wx:if="{{imgUrl}}">
    <image src="{{imgUrl}}"></image>
</block>


// js 代码

chooseImgae(){
    wx.chooseImage({
      success: res=> {
        wx.cloud.uploadFile({
          cloudPath:`${Math.floor(Math.random()*100000)}.png`,
          filePath:res.tempFilePaths[0]
        }).then(res=>{
          this.setData({
            imgUrl:res.fileID
          })
        })
      },
    })
}
```

2. 获取位置

```
chooseLocation(){
    wx.chooseLocation({
      success: (res)=> {
        let locationObj={
          address:res.address,
          latitude:res.latitude,
          longitude:res.longitude,
          name:res.name
        }
        this.locationObj = locationObj;
      },
    })
 },
locationObj:{}
```

3. 显示位置，查看地图

```
data:{
    task:{}
},

onLoad: function (options) {

	// 从云端数据库中拿到存储的位置参数
	
    db.doc(options.id).get().then(res=>{
      this.setData({
        task:res.data
      })
    })
},

viewLocation(){
    wx.openLocation({
      latitude: this.data.task.location.latitude,
      longitude: this.data.task.location.longitude,
      name: this.data.task.location.name,
      address: this.data.task.location.address
    })
}
```