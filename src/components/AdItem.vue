<template>
  <div>
    <p>Sponsored</p>
    <img :src="imgUrl" alt="광고 이미지">
    <p class="title">{{title}}</p>
    <p class="contents">{{contents}}</p>
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
