# minProgram 

## 编程范式

1. 命令式编程 : 原生操作dom

2. 声明式编程 : vue/react/angular



## 界面渲染的整体流程

1. 在渲染层，宿主环境会把wxml转化成对应的js对象

2. 将js对象转成真实dom树，交由渲染层线程渲染

3. 数据变化时，逻辑层提供最新的变化数据，js对象发生改变计较进行diff算法比较

4. 将最新变化的内容反映到真实的dom树中，更新ui



## 获取授权的三种方式

1. 在生命后期中使用 wx.getUserInfo({ success（res）{} }) 获取

2. 通过设置按钮的open-type属性，绑定bindgetuserinfo方法进行获取

	```
	<button 
		size="mini"
		open-type="getUserInfo"
		bindgetuserinfo="handleGetUserInfo"
	></button>
	<!--handleGetUserInfo方法会有一个event参数，参数内包含用户信息-->
	```

3. 通过标签展示
	
	```
	<open-data type="userNickName"></open-data>
	<open-data type="userAvatarUrl"></open-data>
	```



## 注册APP时要进行的事件

1. 判断小程序的进入场景,在 onLaunch 和 onShow 生命周期的回调函数中会有options参数，其中有scene值,用来判断场景

2. 监听生命周期函数，在生命周期函数中执行对应的业务逻辑

3. 因为App()实例只有一个，并且是全局共享的，可以将一些需要共享的全局对象存储在app中

   ```
   <!-- 在app中存储全局变量 -->
   App({
   	 globalData(){
   	 	name:"全局name"
   	 }
   	})


   <!--在子页面中调用-->
   const app=getApp()
   const name=app.globalData.name;
   ```


> tabBar 有一个独立的属性，selectedColor 用来控制 tabBar 被选中时文字的颜色

> 有关tabBar和winodw的配置，详见https://developers.weixin.qq.com/miniprogram/dev/reference/configuration/app.html#tabBar



## 注册page时要进行的事件

1. 在生命周期中发送网络请求，从服务器获取数据

2. 初始化一些数据，以方便被wxml引用展示

3. 监听wxml中的事件，绑定对应的事件函数

4. 其他一些监听，如页面滚动，上拉刷新，下拉加载更多等，参考文档:https://developers.weixin.qq.com/miniprogram/dev/reference/api/Page.html

	```
	  onPullDownRefresh: function() {
        // Do something when pull down.
	  },
	  onReachBottom: function() {
	    // Do something when page reach bottom.
	  },
	  onShareAppMessage: function () {
	    // return custom share data when user share.
	  },
	  onPageScroll: function() {
	    // Do something when page scroll
	  },
	```

5. 在data中声明的数据，只能通过this.setData({})更改，语法类似与react


## mustacha语法

> 在微信小程序中，绑定数据用mustacha语法，即 {{ }} , 包括 wx:if 和 wx:for

	```
	<!-- 控制属性 -->
	<view wx:if="{{condition}}"> </view>

	<!-- 关键字 -->
	<checkbox checked="{{false}}"> </checkbox>
	
	<!-- 三元运算 -->
	<view hidden="{{flag ? true : false}}"> Hidden </view>
	
	<!-- 数组 -->
	<view wx:for="{{[zero, 1, 2, 3, 4]}}"> {{item}} </view>
	```



## 引入 wxss 的三种方式

1. 内联样式，设置style

2. 页内样式 class

3. 全局样式 , app.wxss

4. 外联样式 , 使用@import语句可以导入外联样式表，@import后跟需要导入的外联样式表的相对路径，用;表示语句结束

> 注意：@import要写在本页面的wxss文件中，而不是wxml，且要写相对路径



## 内置组件与wxml内容

1. text 

	+ 行内元素，相当于span

	+ selectable属性决定文本内容是否可以被用户选中

	+ space 属性有三个取值，默认是nbsp，还有ensp和emsp取值

	+ decode 属性控制是否解码，decode可以解析的有&nbsp,&lt,&gt,&amp,&ensp

2. view

	+ 块级元素，相当于div

	+ hover-class,hover-stop-propagation,hover-start-time,hover-stay-time四个属性

3. icon 

	
	+ type 属性为icon的类型,取值有: success, success_no_circle, info, warn, waiting, cancel, download, search, clear

	+ size 属性为icon的大小

	+ color 属性为icon的颜色

4. input

	
	+ value 属性为input的默认值

	+ type 属性决定键盘类型，取值有text，number，idcard，digit

	+ maxlength 属性为最大输入长度

	+ placeholder-style 和 placeholder-class 属性可以设置 placeholder 的样式

	+ 绑定bindinput事件监听输入框内容，另外有焦点控制事件bindfocus和bindblur

5. image

	+ lazy-load 图片懒加载，默认为false

	+ show-menu-by-longpress 开启长按图片显示识别小程序码菜单, 默认为false

	+ bindload 事件当图片载入完毕时触发

6. button

    + size 属性可以设置按钮的大小

    + type 属性可以设置button的样式，取值有primary，default和warn 

    + loading 属性可以在按钮上添加加载动画，默认为false，可以使用mustacha语法设置状态切换

    + hover-class 属性控制按钮按下去的样式

    + hover-stop-propagation 属性可以阻止冒泡，默认为false

    + plain 属性设置镂空效果，默认为false

    + open-type 微信开放功能，属性值share触发分享事件，getUserInfo获取用户信息

7. scroll-view

	+ scroll-x 允许横向滚动 ，默认为false

	+ scroll-y 允许纵向滚动 ，默认为false

	+ bindscroll 事件在滚动时触发

8. block标签

	+ block并不是一个组件，仅仅是一个包装元素，不会在页面中做任何渲染，只接受控制属性

	+ 优点: 将需要进行遍历或判断的多个内容进行包裹，提高代码的可读性

9. wx:for 注意事项(wx:for-)

	+ 基本语法: 

	```
		<view wx:for="{{[1,2,3]}}" >
			{{item}} {{index}}
		</view>
	```

	+ 重命名item和index

	```
		<view wx:for="{{[1,2,3]}}" wx:for-item="number" wx:for-index="ind">
			{{number}} {{ind}}
		</view>
	```

10. 绑定自定义属性(data-)

	```
	<button bindtap="test" data-myproperty="测试">测试自定义属性</button>
	```

	```
	test(e){
       console.log(e.currentTarget.dataset.myproperty)
  	}
	```

11. template 语法 , 在没有自定义组件时用template进行代码复用

	```
	<!-- 必须声明name属性 -->
	<template name="test">
    	<view>uuu</view>
  	</template>
  	<!-- 用属性is调用 -->
  	<template is="test"></template>
	```

	```
	<!-- 外部声明模板，可创建一个独立的wxml写入模板内容，再用import导入 -->
	<import src="../../template/tem.wxml" />
	<template is="outerTem"></template>
	```

> 注意，外部创建的仅仅是一个wxml，而不是一个page



## 导入wxml两种方式

+ import导入： 主要负责导入template,但不能进行递归调用
	```
	<import src="../../template.wxml" />
	```  

+ include导入 : 将公共的wxml中的组件抽取到一个文件中，但不能导入template和wxs，可以递归调用，相当于复制代码,适合于导入导航或脚部，但已被组件化替代
	```
	<include src="../../header.wxml"/>
	```



## wxs

> wxs不能调用js中的函数，也不能调用小程序提供的api，是完全隔离的运行环境

> 在wxml中是不能直接调用page中的函数的，所以wxs常用于一些特殊处理，如过滤大小写，时间戳转变等，相当于vue中的过滤器与管道符的使用

	```
	<!-- 创建wxs -->
	<wxs module="my_wxs">
	  var message="我是在wxs定义的变量"

	  function fn(){
	    console.log("我是在wxs定义的函数")
	  }

	  function formatData(data){
	  	return data.toFixed(2)      // 对数据进行保留两位小数操作
	  }

	  module.exports={
	    message:message,
	    fn:fn,
	    formatData:formatData
	  }
	</wxs>

	<!-- 使用wxs -->
	<view style="color:green;">{{my_wxs.message}}</view>
	<view>{{my_wxs.fn()}}</view>
	<view>{{my_wxs.formatData(23.8899900)}}</view>   // 这里的参数可以调用data中的数据，对data中的数据进行操作

	```


> 注意，一定要是声明module才可以调用,且一定要用module.exports导出内容

	```

	<!-- 引入外部wxs，不支持绝对路径 -->
	<wxs src="../../wxs/outer.wxs" module="outer"/>

	<view style="color:red;">{{outer.message}}</view>
	<view>{{outer.fn()}}</view>

	```

>注意，外部创建的仅仅是一个wxs为后缀的文件，而不是一个page



## 常用的一些事件

1. bind:touchstart 手指触摸屏幕

2. bind:touchmove  手指在屏幕上移动

3. bind:touchend   手指离开屏幕

4. bind:tap        手指点击屏幕

5. bind:longpress  手指长按屏幕


## 组件化使用

##### 关于样式:

	+ 组件内的class样式，只对组件内的wxml节点生效，对于引用组件的page页面不生效，同时页面class样式对组件也不会有影响

	+ 为了防止样式的错乱，官方不推荐使用id，属性，标签选择器

	+ 如何让样式互相影响，在component对象中，可以传入一个options属性，其中options属性中有一个styleIsolation属性

		- 默认为isolated，表示启用样式隔离
		
		- apply-shared 表示页面样式将影响到组件内的节点，但组件内的样式不会影响页面

		- shared 表示互相影响

```
Component({
	options:{
		styleIsolation:"shared"
	}
})
```

> 创建component与创建页面不同，组件内的js引用方法为Component，而不是Page({}).  同时，组件内声明方法是在methods对象中

	

##### 使用组件： 在需要引用的页面内通过json文件中的usingComponents注册组件

	```
	{
	  "usingComponents": {
	    "c_first":"../../component/c_first/c_first"
	  }
	}
	```

##### 组件与页面通信

> 用properties传递数据


	// 组件的js文件

	Component({
	  properties: {
	    title:{
	      type:String,
	      value:"我是组件内声明的默认title"
	    }
	  }
	})



	// 组件的wxml文件

	<view> {{title}}</view>



	// 声明后在页面中调用，如果不传递title的值，将会显示默认值

	<c_first title="页面传递的title"/>


> 用externalClasses传递样式

	// 组件内的js文件,注意要传递的class属性不能命名为驼峰，titleclass只能小写

	Component({
	  externalClasses:['titleclass']  
	})



	// 组件内的wxml文件

	<view class="titleclass"> {{title}}</view>
	


	// 声明后在页面中调用，给titleclass传递一个class样式

	<c_first title="页面传递的title" titleclass="red"/>



	// 页面内的wxss文件

	.red{
	  color:red;
	}


> 用 this.triggerEvent()传递自定义事件

1. 在组件内改变寄主页面的数据 (通过组件内容改变页面中data数据，子改父)
	
	```
	// 组件内的wxml
	<button bind:tap="handlePass">自定义事件的使用</button>
	```

	```
	// 组件内的js,可以将组件内data中的属性传递给页面
	Component({
	  data: {
	    name:"monkey",
	    age:18
	  },
	  methods: {
	    handlePass(){
	      this.triggerEvent('increment',{name:this.data.name,age:this.data.age},{})
	    }
	  }
	})
	```

	```
	// 页面的wxml
	<c_second bind:increment="handleTap"/>
	```

	```
	// 页面的js，可通过参数e拿到组件传递过来的数据(name,age)
	Page({
	  data: {
	    counter:0
	  },
	  handleTap(e){
	    this.setData({
	      counter:this.data.counter+1
	    })
	    console.log(e,this.data.counter)
	  }
	})
	```

2. 页面内改变组件数据 (通过页面的方法改变组件内数据，父改子)

	```
	// 组件的wxml
	<view>{{age}}</view>
	```

	```
	// 组件的js	
	Component({
	  methods: {
	    incrementCom(num){
	      this.setData({
	        age:this.data.age+num
	      })
	    }
	  }
	})
	```

	```
	// 页面的wxml,用class或id标记组件，再在js中获取组件
	<button bind:tap="changeData">改变组件内数据</button>
	<c_second class="second-class"/>
	```

	```
	Page({
	  changeData(){
	  	// 获取组件
	    const c_second=this.selectComponent(".second-class")
	    // 调用组件内方法，修改组件内数据
	    c_second.incrementCom(10)
	  }
	})
	```


## 插槽使用的注意事项

> 插槽的基本语法与vue一样，但在使用具名插槽时，需要在component的options属性里声明

	Component({
		options:{
			multipleSlots:true
		}
	})

## 总结:component构造器中的主要内容

	Component({
		properties:{
			title:{
				type:String,
				value:"默认title"
			}
		},

		data:{
			currentTime:2019
		},

		methods:{
			handleTap(){
				console.log("我是组件内的方法")
			}
		},

		options:{
			styleIsolation:shared,
			multipleSlots:true
		},

		externalClasses:["title"],

		observers:{
			title(newVal){
				console.log(newVal)
			}
		},

		pageLifetimes:{
			// 页面生命周期
			show(){

			},
			hide(){

			},
			resize(){

			}
		},

		lifetimes:{
			// 组件生命周期

		}
	})


## 小程序Api


#### 网络请求

	```
	wx.request({	
		url:"",			   // 请求地址
		method:"get",     // 请求方式
		data:"",         // 请求的参数
		success(){
			// 成功的回调函数
		},
		fail(){
			// 失败的回调函数
		}
	})
	```

> 封装请求为promise

	```
	// 新建一个service文件夹，创建一个network.js文件
	export default function request(options){
	  return new Promise((resolve,reject)=>{
	    wx.request({
	      url: options.url,
	      method:options.method || 'get',
	      data:options.data || {},
	      success:resolve,
	      fail:reject
	    })
	  })
	}
	```

	```
	// 在其他文件使用
	import request from '../../service/network.js'
	Page({
	  onLoad(){
	    request({
	      url:"https://jsonplaceholder.typicode.com/posts"
	    }).then(res=>{
	      console.log(res)
	    })
	  }
	})
	```

#### 交互

1. wx.showToast()

	```
	wx.showToast({
	      title: '正在加载',   // 提示标题
	      icon: 'loading',   // icon图标，有success和loading两个选项
	      image: '',        // 自定义icon
	      duration: 1500,   // 显示时长
	      mask: true,  // 是否显示透明蒙层，防止触摸穿透
	      success: function(res) {},  // 成功回调
	      fail: function(res) {},     // 失败回调
	      complete: function(res) {}, // 无论何时回调
	})
	```

2. wx.showModal()

	```
	wx.showModal({
	      title: '提示框',
	      content: '是否确认离开本页面',
	      showCancel: true,   // 是否展示取消按钮
	      cancelText: '取消',     // 取消按钮显示的文字
	      cancelColor: '',    // 取消按钮的文字颜色
	      confirmText: '确认',    // 确认按钮的文字
	      confirmColor: '',   // 确认按钮的文字颜色
	      success: function (res) {  // 成功回调
	        // 通过res里的两个属性判断点击了哪个按钮. 
	        if(res.cancel){
	          console.log("点击了取消")
	        }
	        if(res.confirm){
	          console.log("点击了确定")
	        }
	      },  
	      fail: function(res) {},     // 失败回调
	      complete: function(res) {},  // 无论何时回调
	})
	```

3. wx.showLoading() & wx.hideLoading()

	```
	wx.showLoading({
	      title: '正在生成报告',    // 显示标题内容
	      mask: true,   // 是否显示遮罩层
	      success: function (res) {    // 成功回调
	        setTimeout(() => {
	          wx.hideLoading()
	        }, 3000)
	      },
	      fail: function (res) { },     // 失败回调
	      complete: function (res) { },  // 无论何时回调
	})
	```

4. wx.showActionSheet()  底部菜单栏

	```
	wx.showActionSheet({
      itemList: ['A', 'B', 'C'],   // 菜单选项
      itemColor:"green",           // 选项颜色
      success(res) {
        console.log(res.tapIndex)
      },
      fail(res) {
        console.log(res.errMsg)
      }
    })
	```

#### 分享

1. open-type  当open-type的值为share时，会触发 Page.onShareAppMessage，所以分享框的样式根据onShareAppMessage内设置而定

```
<button open-type="share">分享</button>
```

2. Page.onShareAppMessage  生命周期函数

	```
	onShareAppMessage(res){
		// from属性代表转发方式，有button和menu两个可选值
	    if (res.from === 'button') {
	      // 来自页面内转发按钮
	      console.log(res.target)
	    }
	    return {
	      title: '自定义转发标题',
	      path: '/page/test?id=123',
	      imageUrl:"http://pic13.nipic.com/20110409/7119492_114440620000_2.jpg"   // 转发时显示的图片
	    }
	}
	```

#### 跳转

1. navigator

> 跳转时的数据交互可以在两个生命周期中完成，onLoad 可以接收原页面传递过来的值，onUnload 可以在跳转时对目标页面进行操作

	```

	// 跳转的时候将name和age同时传递到test页面,然后在test页面的onLoad生命周期中获取

	<navigator url="/pages/test/test?name=monkey&age=999"></navigator>

	// test页面获取跳转传过来的值. 跳转到目标页面时，可操作目标页面，包括setData({})以及其他一些方法

	Page({ 
	  onLoad(options){
	    console.log(options)
	  },
	  onUnload(){
		const pages=getCurrentPages();     // 获取pages对象
		const toast=pages[pages.length-2];  // 取出目标页面对象
		toast.showLoading()                // 调用目标页面的方法
	  }
	})
	```
	
	```
	// 默认返回上级目录,delta代表回退的层数

	<navigator open-type="navigateBack">返回</navigator>
	```

- open-type的几个属性

	+ redirect : 关闭当前页面，跳转到应用内的某个页面，但是不允许跳转到tabBar页面

	+ switchTab : 跳转到tabBar页面，并关闭其他非tabBar页面

	+ reLaunch ： 关闭所有页面，打开应用中的某个页面

	+ navigateBack : 页面回退 ，delta属性代表回退的层数

2. 内置函数跳转

	+ wx.navigateTo 

	```
	wx.navigateTo({
		url:"",
		success(res){
		}
	})
	```

	+ wx.redirectTo

	```
	wx.redirectTo({
		url:"",
		success(res){
		}
	})
	```

	+ wx.navigateBack

	```
	wx.navigateBack({
	  delta: 2
	})
	```