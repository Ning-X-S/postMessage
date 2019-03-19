<template>
  <div id="app">
    <div class="iframe_box">
      <iframe id="iframe" style="{'height:' height + 'px'}" ref="iframe" :src="src"></iframe>
    </div>
    <el-button type="primary" plain @click="sendMessage">向iframe发送信息</el-button>
    <div class="shadow_box" v-if="shadowInfo.isSelected" :style="shadowInfo.style" />
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'app',
  props: {
    msg: String
  },
  data () {
    return {
      src:'http://localhost:8081/',
      iframeWin: {},
      shadowInfo: {
        isSelected: false,
        style: {
          top: 0,
          left: 0,
          height: 0
        }
      },
      iframeBoxTop: 0,
      scrollTop: 0,
      height: 500
    }
  },
  methods: {
    sendMessage() {
      let comList = [
        {
          comName: 'TextCom',
          comId: '1',
          data: {
            text: '这是数据'
          } 
        },
        {
          comName: 'TextCom',
          comId: '2',
          data: {
            text: '这是数据222'
          } 
        },
        {
          comName: 'ImageCom',
          comId: '3',
          data: {
            src: 'https://static.lehe.com/higo/feseTT/www/welcome_3/chanel.png'
          } 
        }
      ]
      this.iframeWin.contentWindow.postMessage({
        cmd: 'getFormJson',
        params: {
          name: "标题1111",
          comList: comList
        }
      }, 'http://localhost:8081/')
    },
    scroll (e) {
      console.log(e)
      this.scrollTop = e.target.scrollTop
    }
  },
  created () {
  },
  mounted () {
    let self = this
    self.$nextTick(_ => {
      console.log('nextTick')
    })
    self.iframeWin = self.$refs.iframe
    self.iframeBoxTop = self.$refs.iframe.offsetParent.offsetTop - 250
    window.addEventListener("message", (event) => {
      let data = event.data
      switch (data.cmd) {
        case "selectedCom":
          // 处理业务逻辑
          alert(data.params.index)
          break;
        case "selectedShadow":
          self.shadowInfo.isSelected = data.params.isSelected
          let shadowHeight, shadowTop
          shadowTop = data.params.top
          if (data.params.height + data.params.top > self.height ) {
            // 当选中的组件在iframe可视范围底部，并无法正常显示完时
            shadowHeight = self.height  - data.params.top
          } else {
            shadowHeight = data.params.height
          }
          if (data.params.top < 0) {  
            // 当选中的组件在iframe可视范围顶部，并无法正常显示完时
            shadowTop = 0 
            shadowHeight = data.params.height + data.params.top
          }
          self.shadowInfo.style.top = Number(self.iframeBoxTop) + Number(shadowTop) - Number(self.scrollTop) + 'px'
          self.shadowInfo.style.height = shadowHeight + 'px'
          break;
        default:
          break;
      }
    });
  }
}
</script>

<style>
html,body {
  height: 100%;
  width: 100%;
  background: #f5f5f7;
  margin: 0;
  position: relative;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.iframe_box {
  height: 500px;
  width: 100%;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 99; 
  border-radius: 8px;
  box-shadow: 0 0 4px rgba( 0, 0, 0, .04);
  overflow: auto;
}
#iframe {
  border: none;
  height: 500px;
  width: 375px;
}
.shadow_box {
  background: #ededef;
  width: 100%;
  position: absolute;
  z-index: 9;
}
</style>
