<template>
  <div id="app">
    <div class="app_box">
      <!-- <div class="box">
        
        <div :class="['layout', item.isSelect ? 'curr' : '']"
            ref="views"
            @mouseover="whereMouse($event, index)"
            @click.stop="interacBack(index)" ></div>
      </div> -->
      <!-- <HelloWorld :msg="msg" /> -->
      <ImageCom :info="data" />
      <keep-alive  v-for="(item, index) in allComponents"  :key="index" >
        <div class="box" :index="index">
          <component :is="item.comName" :info="item.data" />
          <div :class="['layout', item.isSelect ? 'curr' : '']"
            ref="views"
            @mouseover="whereMouse($event, index)"
            @click.stop="interacBack(index)"
          ></div>
        </div>
      </keep-alive>
    </div>
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
import ImageCom from "./components/ImageCom.vue";
import TextCom from "./components/TextCom.vue";
const address = "http://localhost:8080/";

export default {
  name: "app",
  components: {
    HelloWorld,
    ImageCom,
    TextCom
  },
  data() {
    return {
      msg: "Installed CLI Plugins",
      src: "../assets/logo.png",
      allComponents: [],
      scrollTop: 0,
      data: {
        src: "https://static.lehe.com/higo/feseTT/www/welcome_3/chanel.png"
      }
    };
  },
  created() {
    let self = this;
    // 接受父页面发来的信息
    self.$nextTick(_ => {
      window.addEventListener("message", event => {
        let data = event.data;
        switch (data.cmd) {
          case "getFormJson":
            // 处理后台back给内部html发送的组件数据
            self.msg = data.params.name;
            self.allComponents = self.allComponents.concat(data.params.comList);
            break;
          case "selectedCom":
            // 处理业务逻辑
            break;
          default:
            break;
        }
      });
      window.addEventListener("scroll", event => {
        // 监听内部html滚动的scrolltop
        this.scrollTop = event.target.documentElement.scrollTop;
        // 当用户选择的有组件时，页面滑动时，选中框也触发滚动
        // console.log(this.allComponents)
        this.allComponents.forEach((item, idx) => {
          if (item.isChosen) {
            this.choseShadow(idx);
          }
        });
      });
    });
  },
  methods: {
    // postMessage参数封装
    postMessageFun(eventName, params, addresss) {
      window.parent.postMessage(
        {
          cmd: eventName,
          params: params
        },
        addresss
      );
    },
    interacBack(index) {
      if (this.allComponents[index].isSelect) {
        this.allComponents[index].isSelect = false;
      } else {
        this.allComponents[index].isSelect = true;
      }
      this.postMessageFun("selectedCom", index, address);
    },
    whereMouse(e, idx) {
      this.allComponents.forEach(item => {
        item.isChosen = false;
      });
      this.allComponents[idx].isChosen = true;
      // 划过组件式触发事件，用户选中组件给提示阴影
      this.choseShadow(idx);
    },
    // 选中触发postMessage
    choseShadow(idx) {
      let self = this;
      let params = {
        isSelected: true,
        top: self.$refs.views[idx].offsetParent.offsetTop - self.scrollTop, // 当前组件距离当前html顶部的距离减去html滑动的距离
        height: self.$refs.views[idx].offsetHeight // 当前组件的高度
      };
      this.postMessageFun("selectedShadow", params, address);
    }
  }
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
}
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 500px;
  width: 375px;
}
.app_box {
  overflow: hidden;
}
body {
  margin: 0;
  background: #fff;
}
.box {
  position: relative;
}
.layout {
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  z-index: 999;
  background: #fff;
  opacity: 0;
}
</style>
