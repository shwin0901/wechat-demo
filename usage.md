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

   

   

   

   