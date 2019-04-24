<template>
  <router-link class="content-item" :to="{name: 'Detail', params: {id: no}}">
    <p class="category">{{categoryName}}</p>
    <p class="no">{{no}}</p>
    <p class="email">{{email}}</p>
    <p class="date">{{update}}</p>
    <p class="title">{{title}}</p>
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
