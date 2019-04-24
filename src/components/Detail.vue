<template>
  <div class="detail">
    <div class="article">
      <h2 class="title">{{title}}</h2>
      <p>{{email}}</p>
      <p>{{date}}</p>
      <p>{{contents}}</p>
    </div>
    <ul class="replies" v-if="replies.length">
      <li v-for="reply in replies" :key="reply.no">
        <p>{{reply.email}}</p>
        <p>{{reply.updated_at}}</p>
        <p>{{reply.contents}}</p>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Detail',
  created () {
    axios.get(`http://comento.cafe24.com/detail.php?req_no=${this.$route.params.id}`)
      .then(function ({data}) {
        const { article, replies } = data.detail
        this.title = article.title
        this.email = article.email
        this.date = article.updated_at
        this.contents = article.contents
        this.replies = replies
      }.bind(this))
      .catch(error => {
        console.log(error)
      })
  },
  data () {
    return {
      title: '',
      email: '',
      contents: '',
      date: '',
      replies: []
    }
  }
}
</script>

<style scoped>
</style>
