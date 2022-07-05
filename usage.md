#	wechat小程序 基本使用

### 获取用户信息

1. 基础库 2.10.4 支持 wx.getUserProfile 方法获取用户信息，并展示获取用户信息弹窗（wx.getUserInfo不再展示弹窗，获取的用户信息为匿名数据）

   * Index.wxml

   ```html
     <view wx:if="{{userInfo.avatarUrl}}" class="userInfo">
       <image class="indexImg" src="{{userInfo.avatarUrl}}"></image>
       <text>{{userInfo.nickName}}</text>
     </view>
   	<button wx:else bindtap="getUserProfile">获取用户数据</button>
   ```

   * Index.js

   ```javascript
   Page({
     data: {
       userInfo: {}, //用户信息
     },
     
     // 弹窗，获取用户信息
     getUserProfile() {
       wx.getUserProfile({
         success: (res) => {
           this.setData({
             userInfo: res.userInfo
           });
           
           //将用户信息存储在storege里
           try{
   	        wx.setStorageSync('userInfo', res.userInfo)          
           } catch(e) {}
         }
       })
     },
     
      //获取用户存储信息
     getStorageUserInfo() {
       try{
         const userInfo = wx.getStorageSync('userInfo')
         if(userInfo) {
           this.setData({
             userInfo
           })
         }
       } catch(err) {
         console.log('获取用户存储信息失败')
       }
     },
     
     //生命周期函数--监听页面加载
     onLoda() {
     	this.getStorageUserInfo()
     }
   })
   ```

   

   ### input事件绑定

   1. 元素可以通过**id**、**dataset**、**touches**携带信息

   ```html
   <input id="tapTest" data-test="test" bindinput="handleInput" />
   ```
   
   
   
   2. 在相应的 Page.js 写上相应的事件处理函数，参数是event
   
   ```javascript
   Page({
       handleInput(event){
           //通过event.currentTarget.id 获取对应的id
           console.log(event.currentTarget.id)
           
           //通过event.currentTarget.dataset.type获取data-test设置的信息
           console.log(event.currentTarget.dataset.type)
           
           //通过event.detail.value获取input的值
           console.log(event.detail.value)
       }
   })
   ```
   
   
   
   3. evnet对象
   
      * **currentTarget：如果该元素通过事件代理方式在父元素统一处理，那么currentTarget属性表示当前元素的属性，而target属性不一定表示当前元素的属性**
      * **detail：通过event.detail获取元素的value**
      * **特殊事件： [canvas](https://developers.weixin.qq.com/miniprogram/dev/component/canvas.html) 中的触摸事件不可冒泡，所以没有 currentTarget**
   
      
   
   4. 冒泡事件
   
      当一个组件上的事件被触发后，该事件会向父节点传递
   
      | 类型               | 触发条件                                                     |
      | ------------------ | ------------------------------------------------------------ |
      | touchstart         | 手指触摸动作开始                                             |
      | touchmove          | 手指触摸后移动                                               |
      | touchcancel        | 手指触摸动作被打断，如来电提醒，弹窗                         |
      | touchend           | 手指触摸动作结束                                             |
      | tap                | 手指触摸后马上离开                                           |
      | longpress          | 手指触摸后，超过350ms再离开，如果指定了事件回调函数并触发了这个事件，tap事件将不被触发 |
      | longtap            | 手指触摸后，超过350ms再离开（推荐使用 longpress 事件代替）   |
      | transitionend      | 会在 WXSS transition 或 wx.createAnimation 动画结束后触发    |
      | animationstart     | 会在一个 WXSS animation 动画开始时触发                       |
      | animationiteration | 会在一个 WXSS animation 一次迭代结束时触发                   |
      | animationend       | 会在一个 WXSS animation 动画完成时触发                       |
      | touchforcechange   | 在支持 3D Touch 的 iPhone 设备，重按时会触发                 |
   
      **注：除上表之外的其他组件自定义事件如无特殊声明都是非冒泡事件，如 [form](https://developers.weixin.qq.com/miniprogram/dev/component/form.html) 的`submit`事件，[input](https://developers.weixin.qq.com/miniprogram/dev/component/input.html) 的`input`事件，[scroll-view](https://developers.weixin.qq.com/miniprogram/dev/component/scroll-view.html) 的`scroll`事件**
   
      
   
   5. 事件绑定
   
      ```html
      <view bindtap="handleClick"> Click here </view>
      
      //可以是一个数据绑定
      <view bindtap="{{ handlerName }}">Click here! </view>
      
      ```
   
      **注：this.data.handlerName 必须是一个字符串，指定事件处理函数名；如果是个空字符串，则这个绑定会失效**。  
   
      
   
   6. 绑定并阻止事件冒泡
   
      除 `bind` 外，也可以用 `catch` 来绑定事件。与 `bind` 不同， `catch` 会阻止事件向上冒泡
   
      ```html
      <view catchtap="handleClick"> Click here </view>
      ```



### 本地存储

* wx.setStorageSync （存储）
* wx.setStorage
* wx.getStorageSync （获取）
* wx.getStorage
* wx.clearStorageSync （清除全部）
* wx.clearStorage
* wx.removeStorageSync （移除指定key）
* wx.removeStorage

#### wx.setStorageSync

将数据存储在本地缓存中指定的 key 中。会覆盖掉原来该 key 对应的内容。除非用户主动删除或因存储空间原因被系统清理，否则数据都一直可用。单个 key 允许存储的最大数据长度为 1MB，所有数据存储上限为 10MB。

```javascript
wx.setStorageSync('key', 'value')
```

#### wx.setStorage (异步)

```javascript
// 开启加密存储
wx.setStorage({
  key: "key",
  data: "value",
  encrypt: true, // 若开启加密存储，setStorage 和 getStorage 需要同时声明 encrypt 的值为 true
  success() {
    wx.getStorage({
      key: "key",
      encrypt: true, // 若开启加密存储，setStorage 和 getStorage 需要同时声明 encrypt 的值为 true
      success(res) {
        console.log(res.data)
      }
    })
  }
})
```



#### wx.revokeBufferURL

根据 URL 销毁存在内存中的数据，参数为需要销毁的二进制数据 URL





### Flex布局

```css
flex-grow: 可拉伸    flex-shrink： 可压缩    flex-basis: 当前元素的宽度

flex:默认值  ==> flex-grow: 0,  flex-shrink: 1,  flex-basis: auto

flex:1  ==> flex-grow: 1,  flex-shrink: 1,  flex-basis: 0%

flex:auto  ==> flex-grow: 1,  flex-shrink: 1,  flex-basis: auto

flex: 1会导致父元素宽度自动为100%
```



| 值     | flex-grow(可伸缩) | flex-shrink(可压缩) | flex-basis(当前元素宽度) |
| ------ | ----------------- | ------------------- | ------------------------ |
| 默认值 | 0                 | 1                   | auto                     |
| 1      | 1                 | 1                   | 0%                       |
| auto   | 1                 | 1                   | auto                     |





### video组件播放

**属性**

| 属性        | 类型                    | 必填 | 说明                                                       |
| ----------- | ----------------------- | ---- | ---------------------------------------------------------- |
| src         | string                  | 是   | 要播放视频的资源地址，支持网络路径、本地临时路径、云文件ID |
| autoplay    | boolean                 | 否   | 是否自动播放                                               |
| loop        | boolean                 | 否   | 是否循环播放                                               |
| muted       | boolean                 | 否   | 是否静音播放                                               |
| title       | string                  | 否   | 视频的标题，全屏时在顶部展示                               |
| bindplay    | eventhandle（回调函数） | 否   | 当开始/继续播放时触发 play 事件                            |
| bindpause   | eventhandle（回调函数） | 否   | 当暂停播放时触发 pause 事件                                |
| bindended   | eventhandle（回调函数） | 否   | 当播放到末尾时触发 ended 事件                              |
| bindwaiting | eventhandle（回调函数） | 否   | 视频出现缓冲时触发                                         |
| binderror   | eventhandle（回调函数） | 否   | 视频播放出错时触发                                         |



#### VideoContext实例

**可通过 [wx.createVideoContext](https://developers.weixin.qq.com/miniprogram/dev/api/media/video/wx.createVideoContext.html) 获取，[VideoContext](https://developers.weixin.qq.com/miniprogram/dev/api/media/video/VideoContext.html) 通过 `id` 跟一个 [video](https://developers.weixin.qq.com/miniprogram/dev/component/video.html) 组件绑定，操作对应的 [video](https://developers.weixin.qq.com/miniprogram/dev/component/video.html) 组件**

**方法**

| 方法                                                         | 描述     |
| ------------------------------------------------------------ | -------- |
| [VideoContext.play()](https://developers.weixin.qq.com/miniprogram/dev/api/media/video/VideoContext.play.html) | 播放视频 |
| [VideoContext.pause()](https://developers.weixin.qq.com/miniprogram/dev/api/media/video/VideoContext.pause.html) | 暂停视频 |
| [VideoContext.stop()](https://developers.weixin.qq.com/miniprogram/dev/api/media/video/VideoContext.stop.html) | 停止视频 |
| [VideoContext.exitFullScreen()](https://developers.weixin.qq.com/miniprogram/dev/api/media/video/VideoContext.exitFullScreen.html) | 退出全屏 |

**使用案例**

```html
<video
    src="{{url}}"
    bindplay="handlePlay"
    id="{{vid}}"
    object-fit="cover"
    bindended="handleEnded"
></video>
```

```javascript
Page(
    handlePlay(event){
    	const vid = event.currentTarget.id;
    	
    	// 通过上一个id与当前id，上个实例对象是否存在，从而关闭上一个播放的视频
		this.vid !== vid && this.videoContext && this.videoContext.stop();
    
    	// 通过video的id创建控制video标签的实例对象
    	this.videoContext = wx.createVideoContext(vid);		
    },
)
```

**单例模式：**

1. 需要创建多个对象的场景下，通过一个变量接收，始终保持只有一个对象
2. 节省内存空间



### scorll-view

可滚动视图区域。使用竖向滚动时，需要给[scroll-view](https://developers.weixin.qq.com/miniprogram/dev/component/scroll-view.html)一个固定高度，通过 WXSS 设置 height

| 属性                 | 类型              | 说明                                                         |
| -------------------- | ----------------- | ------------------------------------------------------------ |
| scroll-x             | boolean           | 允许横向滚动                                                 |
| scroll-y             | boolean           | 允许纵向滚动                                                 |
| enable-flex          | boolean           | 启用 flexbox 布局。开启后，当前节点声明了 `display: flex` 就会成为 flex container，并作用于其孩子节点。 |
| bindrefresherrefresh | eventhandle(回调) | 自定义下拉刷新被触发                                         |
| refresher-enabled    | boolean           | 开启自定义下拉刷新                                           |
| refresher-triggered  | boolean           | 设置当前下拉刷新状态，true 表示下拉刷新已经被触发，false 表示下拉刷新未被触发 |
| bindscrolltolower    | eventhandle(回调) | 滚动到底部/右边时触发                                        |
| bindscrolltoupper    | eventhandle(回调) | 滚动到顶部/左边时触发                                        |





### 背景音频[BackgroundAudioManager](https://developers.weixin.qq.com/miniprogram/dev/api/media/background-audio/BackgroundAudioManager.html)

##### 功能描述

wx.getBackgroundAudioManager()获取**全局唯一**的背景音频管理器。 小程序切入后台，如果音频处于播放状态，可以继续播放。但是后台状态不能通过调用 API 操纵音频的播放状态。若需要在小程序切后台后继续播放音频，需要在 [app.json](https://developers.weixin.qq.com/miniprogram/dev/reference/configuration/app.html) 中配置 `requiredBackgroundModes` 属性。

##### requiredBackgroundModes 申明需要后台运行的能力，类型为数组。目前支持以下项目：

- `audio`: 后台音乐播放
- `location`: 后台定位

```json
{
  "pages": ["pages/index/index"],
  "requiredBackgroundModes": ["audio", "location"]
}
```



#### 实例

**`let example = wx.getBackgroundAudioManager()`**

##### 返回值

| 属性         | 类型    | 描述                                                         |
| ------------ | ------- | ------------------------------------------------------------ |
| src          | string  | 音频的数据源。默认为空字符串，**当设置了新的 src 时，会自动开始播放**，目前支持的格式有 m4a, aac, mp3, wav。 |
| startTime    | number  | 音频开始播放的位置（单位：s）。                              |
| title(必填)  | string  | 音频标题，用于原生音频播放器音频标题（必填）。原生音频播放器中的分享功能，分享出去的卡片标题，也将使用该值。 |
| playbackRate | number  | 播放速度。范围 0.5-2.0，默认为 1。                           |
| currentTime  | number  | 当前音频的播放位置（单位：s），只有在有合法 src 时返回。     |
| paused       | boolean | 当前是否暂停或停止。                                         |



##### 实例方法

| 方法                                                         | 描述                                                     |
| ------------------------------------------------------------ | -------------------------------------------------------- |
| [example.play()](https://developers.weixin.qq.com/miniprogram/dev/api/media/background-audio/BackgroundAudioManager.play.html) | 播放音乐                                                 |
| [example.pause()](https://developers.weixin.qq.com/miniprogram/dev/api/media/background-audio/BackgroundAudioManager.pause.html) | 暂停音乐                                                 |
| [example.seek(number currentTime)](https://developers.weixin.qq.com/miniprogram/dev/api/media/background-audio/BackgroundAudioManager.seek.html) | 跳转到指定位置                                           |
| [example.stop()](https://developers.weixin.qq.com/miniprogram/dev/api/media/background-audio/BackgroundAudioManager.stop.html) | 停止音乐                                                 |
| [example.onPlay(function callback)](https://developers.weixin.qq.com/miniprogram/dev/api/media/background-audio/BackgroundAudioManager.onPlay.html) | 监听背景音频播放事件                                     |
| [example.onPause(function callback)](https://developers.weixin.qq.com/miniprogram/dev/api/media/background-audio/BackgroundAudioManager.onPause.html) | 监听背景音频暂停事件                                     |
| [example.onStop(function callback)](https://developers.weixin.qq.com/miniprogram/dev/api/media/background-audio/BackgroundAudioManager.onStop.html) | 监听背景音频停止事件                                     |
| example.onTimeUpdate()                                       | 监听背景音频播放进度更新事件，只有小程序在前台时会回调。 |
| example.onEnded()                                            | 监听背景音频自然播放结束事件                             |





### 小程序App.js

注册小程序。接受一个 `Object` 参数，其指定小程序的生命周期回调等。

**App() 必须在 `app.js` 中调用，必须调用且只能调用一次。不然会出现无法预期的后果。**

1. 在App.js设置全局状态属性

   ```javascript
   App({
         /**
      * 当小程序初始化完成时，会触发 onLaunch（全局只触发一次）
      */
     onLaunch: function () {
     },
       
     globalData: {
         isMusicPlay: false,
         musicId: ''
     }
   })
   ```

2. 获取到小程序全局唯一的 `App` 实例。

   ```javascript
   // index.js
   const appInstance = getApp();
   
   // 设置全局实例状态属性值
   appInstance.isMusicPlay = true;
   appInstance.musicId = 1;
   
   // 获取全局实例状态属性值
   console.log(appInstance.globalData.isMusicPlay) // 获取isMusicPlay
   console.log(appInstance.globalData.musicId) // 获取musicId
   ```

   #### 注意事项

   - 不要在定义于 `App()` 内的函数中，或调用 `App` 前调用 `getApp()` ，使用 `this` 就可以拿到 app 实例。
   - 通过 `getApp()` 获取实例之后，不要私自调用生命周期函数。
