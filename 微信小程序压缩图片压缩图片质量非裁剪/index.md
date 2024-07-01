# 微信小程序压缩图片，压缩图片质量非裁剪


#### 原理：利用canvas来实现，将图片绘制到canvas上，然后canvas转图片时，微信提供的一个方法wx.canvasToTempFilePath(Object object, Object this)，此方式可以指定生成图片的质量

#### 注意：quality设置只对jpg格式的图片有效，使用时要将fileType设置为“jpg”， 此举可能会导致其它格式的图片变为jpg格式

xwml
```
//xwml
    <view>
        <button bindtap="chooseImage">选择图片</button>
    </view>
    <!-- 展示压缩后的图片 -->
    <view style="display: flex;justify-content: center;flex-direction: column">
        <image mode="widthFix" src="{{imagePath}}"></image>
    </view>
    <button wx:if="{{imagePath.length>0}}" bindtap="save">点击下载压缩后的图片</button>
    <!-- 用来渲染的canvas -->
    <canvas canvas-id='attendCanvasId' class='myCanvas' style='width:{{cWidth}}px;height:{{cHeight}}px;position: fixed;top: -9999px;left: -9999px;'></canvas>
```

js

```
Page({
/** * 页面的初始数据 */
  data: {
     imagePath: '',
     quality: 0.2,
     cWidth: 0,
     cHeight: 0,
     timer: null
   },
 /** * 生命周期函数--监听页面加载 */
  onLoad: function (options) {
  },
   /** * 选项添加图片事件 */
  chooseImage: function (e) {
    var that = this;
    wx.chooseImage({
    sizeType: ['compressed'], //可选择原图或压缩后的图片
    sourceType: ['album', 'camera'], //可选择性开放访问相册、相机
    success: result => {
        wx.getImageInfo({
            src: result.tempFilePaths[0],
        success: function (res) {
            console.log(res.width, res.height)
        that.setData({
            cWidth: res.width,
            cHeight: res.height }, () => {
            // setData引起的页面渲染完成之后的回调函数
           // setData渲染是异步的 canvasToTempFilePath的时候canvas的大小可能还没有被重新设置
           that.getCanvasImg(result.tempFilePaths, res.width, res.height, that.data.quality);
        })
     }
  })
 }
})
},
  /** * 质量压缩 */
  getCanvasImg(tempFilePaths, canvasWidth, canvasHeight, quality) {
     var that = this;
     const ctx = wx.createCanvasContext('attendCanvasId');
     ctx.clearRect(0, 0, canvasWidth, canvasHeight);
     let pixelRatio = wx.getSystemInfoSync().pixelRatio ctx.drawImage(tempFilePaths[0], 0, 0, canvasWidth, canvasHeight);
     ctx.draw(false, function () {
     that.data.timer = setTimeout(() => {
        wx.canvasToTempFilePath({
        canvasId: 'attendCanvasId',
        fileType: 'jpg', quality: quality,
        destWidth: canvasWidth,
        destHeight: canvasHeight,
        success: function success(res) {
        clearTimeout(that.data.timer)
        that.setData({
           imagePath: res.tempFilePath
        });
     },
     fail: function (e) {
     clearTimeout(that.data.timer)
     wx.showModal({ title: '提示', content: JSON.stringify(e),
     })
    }
   });
   }, 500)
  });
 },
   /** * 图片保存到相册 */
   save(e) {
      let that = this;
      wx.saveImageToPhotosAlbum({
          filePath: that.data.imagePath,
          success: function (res) {
         console.log('图片已保存');
     },
      fail: function (res) {
        console.log('保存失败');
    }
    })
   }
 })
```
