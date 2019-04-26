<template>
  <router-link class="content-item" :to="{name: 'Detail', params: {id: no}}">
    <div class="content-header row justify-content-between border-bottom p-2">
      <p class="category">{{categoryName}}</p>
      <p class="no">No. {{no}}</p>
    </div>
    <div class="row p-2 pl-4">
      <p class="email pr-2 border-right">{{email}}</p>
      <p class="date pl-2">{{update}}</p>
    </div>
    <h2 class="title h2 text-truncate pl-2" :title="title">{{title}}</h2>
    <p class="contents p-2" :title="contents">{{contents.length > 450 ? contents.slice(0, 450) + '...' : contents}}</p>
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
  .ie9 {
    .content-item {
      .content-header {
        .category {
          float: left;
        }
        .no {
          float: right;
        }
      }
      .content-header:after {
        content: '';
        display: block;
        clear: both;
      }
      .email, .date {
        display: inline-block;
      }
    }
  }
</style>
