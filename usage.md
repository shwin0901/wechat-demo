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