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

