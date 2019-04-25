<template>
  <router-link class="content-item" :to="{name: 'Detail', params: {id: no}}">
    <div class="row justify-content-between border-bottom">
      <p class="category col-2">{{categoryName}}</p>
      <p class="no col-1">{{no}}</p>
    </div>
    <div class="row pt-2 pb-2">
      <p class="email col-2 border-right">{{email}}</p>
      <p class="date col-3">{{update}}</p>
    </div>
    <p class="title h2">{{title}}</p>
    <p class="contents">{{contents}}</p>
  </router-link>
</template>
<script>
export default {
  name: 'ContentItem',
  props: {
    categoryList: Array,
    category: String,
    contents: String,
    email: String,
    no: String,
    title: String,
    update: String
  },
  computed: {
    categoryName () {
      return this.categoryList.filter(function (category) {
        return category.no === this.category
      }.bind(this))[0].name
    }
  }
}
</script>
<style lang="less" scoped>
  .content-item {
    display: block;
  }
</style>
