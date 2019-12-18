# 云开发

## 创建云函数

1. 在project.config.json文件中添加配置cloudfunctionRoot

```
{
  "description": "项目配置文件",
  "cloudfunctionRoot":"./cloud"

  //  "./cloud"为云函数文件夹名，在配置中声明后会自动识别为云函数
}
```

## 服务端使用云函数

```
// 云函数入口文件
const cloud = require('wx-server-sdk')

cloud.init()

// 云函数入口函数，event中包含前台传递过来的数据，context中包含一些用户信息
exports.main = async (event, context) => {
  let {a,b}=event;

  return {
    sum:a+b
  }
}
```

## 小程序端使用云函数

```
// 先在小程序端app.js内初始化云函数

App({
  onLaunch: function () {
    wx.cloud.init({
    	env:"henry",
    	traceUser:true     // 是否能够在控制台查看用户信息
    })
  }
})
```

```
// 在使用页面调用
Page({
  onLoad: function () {
   // 调用云函数
    wx.cloud.callFunction({
      name:"sum",
      data:{
        a:2,
        b:3
      }
    }).then(res=>{
      console.log(res.result)
    })
  }
})
```

## 数据库使用

> 数据库使用注意更改权限问题

+ doc获取数据

	```
	const myTodo = db.collection('todos').doc('my-todo-id')   方法接受一个 id 参数，指定需引用的记录 ID
	```

+ get获取数据

	```
	const cloud = require('wx-server-sdk')
	cloud.init({
	  env: cloud.DYNAMIC_CURRENT_ENV
	})
	const db = cloud.database()
	exports.main = async (event, context) => {
	  return await db.collection('todos').where({
	    _openid: 'xxx' // 填入当前用户 openid
	  }).get()
	}
	```

+ count 查询总数
```
async (event, context) => {
  return await db.collection('todos').where({
    age:_lt(18)
  }).count()
}
```

+ add添加数据

	```
	const cloud = require('wx-server-sdk')
	cloud.init({
	  env: cloud.DYNAMIC_CURRENT_ENV
	})
	const db = cloud.database()
	exports.main = async (event, context) => {
	  try {
	    return await db.collection('todos').add({
	      // data 字段表示需新增的 JSON 数据
	      data: {
	        description: "learn cloud database",
	        due: new Date("2018-09-01"),
	        // 位置（113°E，23°N）
	        location: new db.Geo.Point(113, 23),
	        done: false
	      }
	    })
	  } catch(e) {
	    console.error(e)
	  }
	}
	```

+ update  更新记录（仅更新指定字段）

	```
	const db = cloud.database()
	const _ = db.command
	exports.main = async (event, context) => {
	  try {
	    return await db.collection('todos').where({
	      done: false
	    })
	    .update({
	      data: {
	        progress: _.inc(10)
	      },
	    })
	  } catch(e) {
	    console.error(e)
	  }
	}
	```

+ set  替换更新记录（新字段替换所有字段）

+ remove

	```
	exports.main = async (event, context) => {
	  try {
	    return await db.collection('todos').where({
	      done: true
	    }).remove()
	  } catch(e) {
	    console.error(e)
	  }
	}
	```

+ orderBy

	```
	db.collection('todos')
	    .orderBy('progress', 'desc')
	    .orderBy('description', 'asc')
	    .get()
	```

+ limit 限制查询条数   db.collection('todos').limit(10).get()

+ skip 指定查询返回结果时从指定序列后的结果开始返回，常用于分页  db.collection('todos').skip(10).get()

+ 正则查询(模糊查询)

	```
	db.collection("data").where({
		name: new db.RegExp({
			regexp:" name-0[0-9] ",       // 匹配 name 为 name-01 到 name-09 的数据
			options:"i"
		})
	})
	``` 

+ filed 指定返回结果中记录需返回的字段

	```
	return await db.collection('todos').field({
	      description: true,
	      done: true,
	      progress: true
	    }).get()
	```

+ command 

	+  lt  代表小于

	+  lte 代表小于等于 

	+  gt  代表大于

	+  gte 代表大于等于
	
	+  in  代表范围之内           
	```progress: _.in([0, 100])```
    
    +  nin 代表不在指定范围内     
    ```progress: _.nin([0, 100])```
 	
 	+  and 代表逻辑与            
 	```progress: _.gt(50).and(_.lt(100)) 或 memory: _.and(_.gt(50), _.lt(100))```
    
    +  or  代表逻辑或            
    ```progress: _.gt(80).or(_.lt(20))  或 memory: _.or(_.gt(80), _.lt(20))```

    +  set 代表替换更新          
	```
	db.collection('todos').doc('doc-id').update({
      data: {
        style: _.set({
          color: 'red',
          size: 'large'
        })
      }
	```
	
	+ remove  删除操作
	```
	return await db.collection('todos').doc('todo-id').update({
      data: {
        style: _.remove()
      }
    })
	```

	+ inc 代表自增
	```
	db.collection('todos').doc('todo-id').update({
      data: {
        progress: _.inc(10)
      }
    })
	```

	+ push pop shift unshift 同数组方法