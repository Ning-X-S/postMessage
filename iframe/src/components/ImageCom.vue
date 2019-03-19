<template>
  <div class="image" @click.stop="openGoodsDetils(info)">
   <img alt="图片路径错误了" :src="info.src">
  </div>
</template>

<script>
export default {
  name: 'ImageCom',
  props: {
    info: Object
  },
  data () {
    return {
    }
  },
  methods: {
    openGoodsDetils(info) {
      console.log(info)
    }
  },
  mounted () {
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.image img {
  width: 10rem;
  margin: 0 auto;
  display: block;
}
</style>
