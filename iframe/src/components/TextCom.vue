<template>
  <div class="text">
    {{ info.text }}
  </div>
</template>

<script>
export default {
  name: "TextCom",
  props: {
    info: Object
  },
  // data () {
  //   return {
  //   }
  // },
  // methods: {
  // },
  mounted() {
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 10px 0;
}
</style>
