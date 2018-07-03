<template>
  <div>
    <canvas style="border:1px;position:absolute;top:0;left:0;width:750rpx;height:1334rpx;" canvas-id="tower" disable-scroll="true" @touchstart="bindtouchstart" @touchmove="bindtouchmove" @touchend="bindtouchend" @tap="bindtap" @longpress="bindlongpress"></canvas>
  </div>
</template>

<script>
import { wxDraw, Shape } from '@/libs/wxdraw.min.js'
export default {
  data () {
    return {
      userInfo: {},
      systemInfo: wx.getSystemInfoSync(),
      hookImage: '',
      blockImage: '',
      downBlockImage: '',
      boxs: []
    }
  },
  onLoad () {
    let WxDraw = wxDraw // 按照eslint的规范是一个类的首字母应该大写
    let context = wx.createCanvasContext('tower', this.$mp.page)
    let oringin = {
      x: this.systemInfo.windowWidth / 2,
      y: 0
    }
    let line = 170
    setTimeout(() => {
      this.wxCanvas = new WxDraw(context)
      const hook = new Shape('image', {x: oringin.x, y: oringin.y, w: this.hookImage.width * 0.5, h: this.hookImage.height * 0.5, file: this.hookImage.path}, 'fill', false)
      this.wxCanvas.add(hook)
      hook.animate({'rotate': 60 * Math.PI / 180}, {duration: 500}).animate({'rotate': -60 * Math.PI / 180}, {duration: 1000}).animate({'rotate': 0}, {duration: 500}).start(true)
      // hook.animate({'rotate': 60 * Math.PI / 180}, {duration: 500}).start(1)
      this.hookBox = new Shape('image', {x: oringin.x, y: oringin.y + line, w: this.blockImage.width * 0.5, h: this.blockImage.height * 0.5, file: this.blockImage.path}, 'fill', false)
      this.wxCanvas.add(this.hookBox)
      this.hookBox.setOrigin([oringin.x, 0])
      this.hookBox.animate({'rotate': 60 * Math.PI / 180}, {duration: 500}).animate({'rotate': -60 * Math.PI / 180}, {duration: 1000}).animate({'rotate': 0}, {duration: 500}).start(true)
    // this.hookBox.animate({'x': oringin.x - Math.sin((Math.PI / 180) * 30) * line + this.hookBox.Shape.Option.w / 2, 'y': Math.cos((Math.PI / 180) * 30) * line + this.hookBox.Shape.Option.h / 2}, {duration: 500}).start()
    // box.animate({'x': 130, 'y': 150}, {duration: 250}).animate({'x': 60, 'y': 130}, {duration: 250}).start()
    }, 1000)
  },
  methods: {
    getUserInfo () {
      // 调用登录接口
      wx.login({
        success: () => {
          wx.getUserInfo({
            success: res => {
              this.userInfo = res.userInfo
            }
          })
        }
      })
    },
    getTempFilePath (url) {
      return new Promise((resolve, reject) => {
        wx.downloadFile({
          url: url, // 仅为示例，非真实的接口地址
          success: res => {
            if (res.statusCode === 200) {
              wx.getImageInfo({
                src: res.tempFilePath,
                success: image => {
                  resolve(image)
                }
              })
            } else {
              reject(res.errMsg)
            }
          },
          complete: res => {
            // console.log(res)
          }
        })
      })
    },
    bindtap () {
      const line = 170
      const oringin = {
        x: this.systemInfo.windowWidth / 2,
        y: 0
      }
      const l = this.hookBox.Shape.Option.rotate
      let box = {}
      if (l > 0) {
        box.x = oringin.x - line * Math.sin(l)
        box.y = line * Math.cos(l)
      } else {
        box.x = oringin.x + (line * Math.sin(-l))
        box.y = line * Math.cos(-l)
      }
      console.log(box.x)
      let downBox = new Shape('image', {x: box.x, y: box.y, w: this.downBlockImage.width * 0.5, h: this.downBlockImage.height * 0.5, file: this.downBlockImage.path}, 'fill', true)
      this.wxCanvas.add(downBox)
      const self = this
      downBox.animate({'x': box.x, 'y': this.systemInfo.windowHeight - downBox.Shape.Option.h / 2}, {duration: 500,
        easing: 'easeOutExpo',
        onStart: function () {

        },
        onEnd: function () {
          console.log(this)
          if (self.boxs.length > 0) {
            if (Math.abs(this.atrribute.x - self.boxs[0].atrribute.x)) {

            }
          } else {
            self.boxs[0] = this
          }
        }}).start()
    }
  },

  created () {
    // 调用应用实例的方法获取全局数据
    this.getUserInfo()
    this.getTempFilePath('https://test.cdn.xiaobaoxiu.cn/xbshow/Xcx/activity/201807031416111611-q6y6jc.png').then(image => {
      this.hookImage = image
    })
    this.getTempFilePath('https://cdn.xiaobaoxiu.cn/xbshow/Xcx/activity/201807031020432043-uishyy.png').then(image => {
      this.blockImage = image
    })
    this.getTempFilePath('https://test.cdn.xiaobaoxiu.cn/xbshow/Xcx/activity/201807031742144214-bzoswv.png').then(image => {
      this.downBlockImage = image
    })
  }
}
</script>

<style scoped>
.userinfo {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.userinfo-avatar {
  width: 128rpx;
  height: 128rpx;
  margin: 20rpx;
  border-radius: 50%;
}

.userinfo-nickname {
  color: #aaa;
}

.usermotto {
  margin-top: 150px;
}

.form-control {
  display: block;
  padding: 0 12px;
  margin-bottom: 5px;
  border: 1px solid #ccc;
}

.counter {
  display: inline-block;
  margin: 10px auto;
  padding: 5px 10px;
  color: blue;
  border: 1px solid blue;
}
</style>
