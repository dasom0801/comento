<template>
  <div class="ad-item p-2" role="complementary">
    <p class="mb-2">Sponsored</p>
    <img class="mr-3" :src="imgUrl" alt="광고 이미지">
    <p class="title h2 text-truncate" :title="title">{{title}}</p>
    <p class="contents" :title="contents">{{contents.length > 450 ? contents.slice(0, 450) + '...' : contents}}</p>
  </div>
</template>
<script>
export default {
  name: 'AdItem',
  props: {
    contents: String,
    img: String,
    title: String
  },
  computed: {
    imgUrl () {
      return `http://comento.cafe24.com/public/images/${this.img}`
    }
  }
}
</script>
<style lang="less" scoped>
  img {
    float: left;
    width: 50%;
  }
  @media (max-width: 480px) {
    img {
      float: none;
      margin-bottom: 10px;
      width: 100%;
    }
  }
</style>
