# PictureView
基于 uniapp 框架的图片浏览小程序 

## 项目接口

[接口文档](https://www.showdoc.com.cn/414855720281749?page_id=3678621017219602)

## 1.项目搭建

### 增 tabbar 页面

| 页面名称 | 路径                 |
| -------- | -------------------- |
| 首页     | home/index.vue       |
| 横屏     | horizontal/index.vue |
| 精美视频 | video/index.vue      |
| 搜索     | search/index.vue     |
| 我的     | mine/index.vue       |

### 把icon 文件夹放到 static 文件目录下

### 构建tabbar  (app.json文件)

```js
"tabBar": {
    "color": "#999",
    "selectedColor": "#ff2d4a",
    "backgroundColor": "#fafafa",
    "position": "bottom",
    "borderStyle": "black",
    "list": [
      {
        "pagePath": "pages/home/index",
        "text": "首页",
        "iconPath": "./static/icon/_home.png",
        "selectedIconPath": "./static/icon/home.png"
      },
      {
        "pagePath": "pages/horizontal/index",
        "text": "横屏",
        "iconPath": "./static/icon/_img.png",
        "selectedIconPath": "./static/icon/img.png"
      },
      {
        "pagePath": "pages/video/index",
        "text": "视频",
        "iconPath": "./static/icon/_videocamera.png",
        "selectedIconPath": "./static/icon/videocamera.png"
      },
      {
        "pagePath": "pages/search/index",
        "text": "搜索",
        "iconPath": "./static/icon/_search.png",
        "selectedIconPath": "./static/icon/search.png"
      },
      {
        "pagePath": "pages/mine/index",
        "text": "我的",
        "iconPath": "./static/icon/_my.png",
        "selectedIconPath": "./static/icon/my.png"
      }
    ]
  }
```

### 引入字体图标

- 使用iconfont
- 在App.vue中全局引入

```js
@import "./styles/iconfont.wxss";
@import "./styles/base.wxss";
```

### uni-ui介绍

[官方介绍](https://uniapp.dcloud.io/component/README?id=uniui)

#### 安装

- Hbuilder中，在创建项目时，就可以直接选择uni-ui模板
- 如果在创建项目时选择是默认模板，可以去插件市场安装

#### 引用

在Hbuilder中，所有的uni-ui都会在components目录下，想要使用，哪个，就像使用组件的方式一样

引入-->注册-->使用

![01](http://ww1.sinaimg.cn/large/007WWC4agy1gss8noqzxyj60tf095jvg02.jpg)

### uni-api介绍

[官方介绍](https://uniapp.dcloud.io/api/README)

- 原生的微信小程序的api都是不支持promise
- uni-app对大部分的小程序的原生api做了封装，使之支持promise
- 使用方式
  - 原生微信小程序wx.request
  - uni-app的方式 uni-request
  - 其它的 api 的使用方式也类似

```js
// 默认方式
uni.request({
    url: 'https://www.example.com/request',
    success: (res) => {
        console.log(res.data);
    }
});

// Promise
uni.request({
        url: 'https://www.example.com/request'
    })
    .then(data => {//data为一个数组，数组第一项为错误信息，第二项为返回数据
        var [error, res]  = data;
        console.log(res.data);
    })

// Await
async function request () {
    var [error, res] = await uni.request({
        url: 'https://www.example.com/request'
    });
    console.log(res.data);
}
```

## 2.功能分析

### 修改导航栏的外观

```js
"globalStyle": 
{
	"navigationBarTextStyle": "white",
	"navigationBarTitleText": "手机壁纸大全",
	"navigationBarBackgroundColor": "#000"
},
```

### 使用 **分段器组件** 搭建子页面

- 首页模块分为4个部分，分别是推荐、分类、最新、专辑
- 新建自定义组件来替代上述4个页面
  - home-recommend
  - home-category
  - home-new
  - home-album

[分段器组件](https://ext.dcloud.net.cn/plugin?name=uni-segmented-control)

安装方式

本组件符合[easycom](https://uniapp.dcloud.io/collocation/pages?id=easycom)规范，`HBuilderX 2.5.5`起，只需将本组件导入项目，在页面`template`中即可直接使用，无需在页面中`import`和注册`components`。

如需通过`npm`方式使用`uni-ui`组件，另见文档：https://ext.dcloud.net.cn/plugin?id=55

```js
<template>
	<view>
		<uni-segmented-control :current="current" :values="items.map(v=>v.title)" @clickItem="onClickItem" styleType="text"
		 activeColor="#ff0000"></uni-segmented-control>
		<view class="content">
			<view v-show="current === 0">
				<home-recommend></home-recommend>
			</view>
			<view v-show="current === 1">
				<home-category></home-category>
			</view>
			<view v-show="current === 2">
				<home-new></home-new>
			</view>
			<view v-show="current === 3">
				<home-album></home-album>
			</view>
		</view>
	</view>
</template>
```

```js
	data() {
			return {
				items: [{
						title: "推荐"
					},
					{
						title: "分类"
					},
					{
						title: "最新"
					},
					{
						title: "专辑"
					}
				],
				current: 1
			};
		},
		methods: {
			onClickItem(e) {
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex;
				}
			}
		}
```

### 封装自己异步请求

- 原生请求不支持promise
- uniapp 的请求不能够方便添加  请求中效果
- uni-app 的请求返回值是一个数组，不方便

封装思路

- 基于原生的promise 来封装

  ```js
  export default (params)=>{
  	// 加载中
  	uni.showLoading({
  		title:"加载中"
  	})
  	return new Promise((resolve,reject)=>{
  		wx.request({
  			...params,
  			success(res){
  				resolve(res.data);
  			},
  			fail(err){
  				reject(err)
  			},
  			complete(){
  				uni.hideLoading();
  			}
  		})
  	})
  }
  ```

  

- 挂载到Vue的原型上

  ```js
  import request from './utils/request'
  Vue.prototype.request=request
  ```

  

- 通过this.request 的方式来使用

  ```js
  onLoad() {
  			this.request({
  				url:"http://157.122.54.189:9088/image/v3/homepage/vertical"
  			})
  			.then(res=>{
  				console.log(res);
  			})
  		}
  ```

## 3.首页-推荐模块

### 数据动态渲染

在最外层加上 ``` v-if="recommend.length>0"```

moments.js的使用

### 在Hbuilder中引用moment.js  [moments.js官网](http://momentjs.cn/)
- 推荐使用  moment-多语言支持.min.js
- 这样可以直接使用  moment.locale(String)  来设置语言环境
```js
import moment from "@/utils/moment.js"
//默认是中文格式
//使用中文格式
moment.locale('zh-cn');
// 月份模块
this.months = res.res.homepage[2];
// 将时间戳 改成 18号/月 moment.js
this.months.MM = moment(this.months.stime).format("MM");
this.months.DD = moment(this.months.stime).format("DD");
```

### 数据请求

#### "热门列表的基于scroll-view的分页加载"

- 使用 scroll-view 标签充当分页容器

- 由于需要参数需要变化，data中设置一个全局参数

  ```js
  // 请求的参数
  params:{
  	limit:30,
  	adult:false,
  	first:1,
  	skip:0,
  	order:"hot"
  },
  // 是否还有下一页
  hasMore:true,
  ```

- 判断是否还有下一页

  ```js
  if(res.res.vertical.length===0){
  	this.hasMore=false;
  	return;
  }
  ```

  

- 绑定滚动条触底事件 scrolltolowe

  ```js
  // 滚动条触底事件
  handleToLower(){
  	/* 
  	1.修改参数  skip+=limit
  	2.重新发送请求 getList()
  	3.请求回来成功 hots 数据叠加
  		*/
  	if(this.hasMore){
  	this.params.skip+=this.params.limit;
  	this.getList();
  	}else{
  		// 弹窗提示用户
  		uni.showToast({
  			title:"哎呀 `(*>﹏<*)′ 到底了了哦",
  			icon:"none"
  		})
  	}
  }
  ```

  请求回来成功 hots 数据叠加

  ```js
  //热门模块
  // 使用数组拼接 es6
  this.hots=[...this.hots,...res.res.vertical];
  ```

## 4.专辑列表页

### 使用setNavigationBarTitle 修改 页面标题

```js
mounted() {
	// 修改页面标题
	uni.setNavigationBarTitle({
		title:'最新'
	})
}
```

### 发送请求获取数据

### 使用swiper轮播图组件

- swiper三要素

> ​       **autoplay**
>
> ​       **circular**
>
> ​       **indicator-dots**

- swiper 默认高度 150rpx

- image

> 默认宽度320rpx =>基本样式中重置了100%
>
> 默认高度240rpx

- 计算图片宽度和高度的比例

- 把图片的比例也写到swiper标签样式中

- swiper-item    100%

### 使用scroll-view组件实现分页

### 数据动态渲染

- 在伸缩盒子上，一下这种方式，可能会让盒子撑开
- 最好在其父盒子上加上  ```overflower=hidden```

```js
text-overflow: ellipsis;
overflow: hidden;
white-space: nowrap;
```

- 图片文字居中显示 ```vertical-align: middle;```

### 点击跳转到专辑详情页

- 使用模板字符串 

- 注意  `` 和 ${}是一起使用的

<navigation :url="`/pages/album/index?id=${item.id}`"></navigation>

## 5.专辑详情页

### 发送请求获取数据

- url使用字符串模板语法

```js
getList(){
	this.request({
		url:`http://service.picasso.adesk.com/v1/wallpaper/album/${this.id}/wallpaper`,
		data:this.params
	})
	.then(res=>{
		console.log(res);
		this.album=res.res.album;
	})
},
```

### 使用onReachBottom事件触发  上拉加载下一页

```js
// 触底加载下一页
onReachBottom() {
	if(this.hasMore){
		this.params.skip+=this.params.limit;
		this.getalbum();
	}else{
		uni.showToast({
			title:'没有更多数据了',
			icon:'none'
		})
	}
},
```

### 数据动态渲染

- 若请求到的数据中含有换行符
- 可以使用  <text></text>

## 6.图片详情

### 封装超链接组件 go-detail.vue

- 缓存图片详情页面需要滑动的图片数组和图片索引

- 跳转到图片详情页面

  ```js
  handleClick(){
  // 1.将数据缓存下来
  getApp().globalData.imgList=this.list;
  getApp().globalData.imgIndex=this.index;
  // 2.再实现点击跳转页面
  uni.navigateTo({
  	url:"/pages/img-detail/index"
  })}
  ```

### 发送请求获取数据

使用 moment.js 处理特殊事件格式

```js
res.res.hot.forEach(v=>v.cnTime=moment(v.atime*1000).fromNow());
res.res.comment.forEach(v=>v.cnTime=moment(v.atime*1000).fromNow());
```

### 封装  手势滑动组件

- 手指在容器上产生了移动
  - 手指按下屏幕事件 touchstart
  - 手指离开屏幕事件 touchend
  - 手指在屏幕上的坐标 event.changeTouches[0].clientX 和clientY
- 手指在容器上滑动的时间不能太长
  - 记录按下屏幕的时间
  - 记录离开屏幕的时间
- 根据坐标判断滑动的方向
  - 手指离开屏幕时根据坐标判断滑动的方向

逻辑实现

- 给用户绑定两个触屏事件  touchstart  touchend
- 用户按下屏幕事件
  - 记录按下屏幕的时间 Date.now() 时间戳 返回1970 -1 -1 到现在的毫秒数
  - 记录按下屏幕的坐标 x 和 y
- 用户离开屏幕事件
  - 记录用户离开屏幕时间  Date.now()
  - 记录用户离开屏幕的坐标  x y
  - 根据两个时间 运算判断是否合法 判断 滑动的方向

swiper-action

```js
<template>
	<view @touchstart="handleTouchStart" @touchend="handleTouchEnd">
		<slot></slot>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				// 按下的时间
				startTime:0,
				// 按下的坐标
				startX:0,
				startY:0
			}
		},
		methods:{
			handleTouchStart(e){
				// console.log(e);
				// 手指按下屏幕
				// console.log("按下:"+e.changedTouches[0].clientX);
				// console.log("按下:"+e.changedTouches[0].clientY);
				
				this.startTime=Date.now();
				this.startX=e.changedTouches[0].clientX;
				this.startY=e.changedTouches[0].clientY;
			},
			handleTouchEnd(e){
				// 手指离开屏幕
				// console.log("离开:"+e.changedTouches[0].clientX);
				// console.log("离开:"+e.changedTouches[0].clientY);
				
				const endTime=Date.now();
				const endX=e.changedTouches[0].clientX;
				const endY=e.changedTouches[0].clientY;
				
				// 判断按下的时长
				if(endTime-this.startTime>2000) {
					retrun;
				}
				// 滑动的方向
				let direction="";
				
				// 先判断用户的滑动距离 是否合法  判断滑动的方向 注意。距离要加上绝对值
				if(Math.abs(endX-this.startX)>10){
					// 滑动的方向
					direction=endX-this.startX>0?"right":"left";
				}else {
					return;
				}
				
				// 用户做了合法操作
				// console.log(direction);
				// 向外传递事件
				this.$emit("swiperAction",{direction});
				
			}
		}
	}
</script>

<style>
</style>

```

```js
// 滑动事件
			handleSwiperAction(e){
				/*  
				 1.用户 左滑 imgIndex++
				 2.用户 右滑 imgIndex--
				 3.判断数组是否越界 
				 4.左滑 e.direction==="left" && this.imgIndex<imgList.length-1
				 5.右滑 e.direction==="right" && this.imgIndex>0
				 */
				const {imgList} =getApp().globalData;
				
				if(e.direction==="left" && this.imgIndex<imgList.length-1)
				{
					// 可以左滑 加载到下一页
					this.imgIndex++;
					this.getData();
				}else if(e.direction==="right" && this.imgIndex>0){
					// 右滑 加载上一页的
					this.imgIndex--;
					this.getData();
				}else {
					uni.showToast({
						title:'没有数据啦',
						icon:"none"
					})
				}
			}
		
		}
```



### 下载图片

- downloadFile  下载远程文件到小程序的内存中
- saveImageToPhotosAlbum 将图片从内存中下载到本地

```js
  // 点击下载事件
		async	handleDownLoad(){
				// uni.downloadFile
				// uni.saveImageToPhotosAlbum
				await uni.showLoading({
					title:'下载中'
				});
				// 1.将远程文件下载到小程序的内存中
				const res1 = await uni.downloadFile({url:this.imgDetail.img})
				const {tempFilePath}=res1[1];
				
				// 2.将小程序内存中的 临时文件下载的本地中
				const res2=await uni.saveVideoToPhotosAlbum({
					filePath:tempFilePath
				});
				
				// 3.提示用户下载成功
				uni.hideLoading();
				await uni.showToast({
					title:'下载成功',
				});
				
			}
		}
```

## 7.首页分类

### 发送请求获取数据

### 渲染页面

## 8.图片分类

分段器的使用

- 点击不同按钮，重新发送请求
- 把skip重置为0
- 把vertical重置为0
- 当点击的是同一个标题的时候，什么都不做，return；

发送请求数据

渲染页面

分页加载

- 注意一个 scroll-view中新属性 enable-flex 

  > 当你需要把scroll-view标签做一个伸缩盒子的话，必须加上 enable-flex 属性才会生效

跳转到图片详情页

## 9.视频播放

### 页面渲染

- 分段器的使用
- 发送请求数据
  - 每一个子页面用到的接口地址或者接口参数都不一样
  - 将每一个页面的接口地址和接口参数都封装到标题数组中
  - 点击标题的时候，也传递对应的接口路径和参数给内容组件
    - 需要在组件汇总使用 "watch" 来监控接受参数发生了变化
    - 参数发生变化  重新发送请求
  - 内容组件接受参数，发送请求渲染页面
- 渲染页面
- 分页加载

### 播放视频

页面渲染

```js
<!-- 视频开始 -->
<view class="video_wrap">
	<video :src="videoObj.video" objectFit="fill" controls></video>
</view>
<!-- 视频结束 -->
```

播放视频

开关声音

- video 标签的 muted 属性实现声音的开关

  ```js
  <!-- 工具栏开始 -->
  <view class="video_tool">
  	<view @click="handleMuted" :class="['iconfont',muted?'iconjingyin':'iconshengyin']"></view>
  		<view class="iconfont iconzhuanfa">
  			<button open-type="share"></button>
  		</view>
  	</view>
  ```

  

- button 的 open-type 设置为 share 实现转发

  样式  opacity: 0; 透明度为0

  ```js
  .iconzhuanfa {
  	position: relative;
  
  	button {
  		position: absolute;
  		width: 100%;
  		height: 100%;
  		opacity: 0;
      }
  }
  ```

  

转发

下载视频

```js
// 下载视频
async handleDownLoad() {
	await uni.showToast({
		title: '下载中',
		icon: "none"
	});
	// 1.将远程文件下载到 小程序内存中
	const {
		tempFilePath
	} = (await uni.downloadFile({
		url: this.videoObj.video
	}))[1];
	// 2.将内存中的文件下载到本地上
	await uni.saveVideoToPhotosAlbum({
		filePath: tempFilePath,
	});
				
	await uni.showToast({
	title:'下载成功'
	})
}
```

