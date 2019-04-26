<template>
  <div class="detail card container my-2 pb-2">
    <div class="card-body mb-3">
      <h2 class="title h2">{{title}}</h2>
      <div class="row px-3 mb-2 justify-content-between">
        <p class="email">{{email}}</p>
        <p class="date">{{date}}</p>
      </div>
      <p>{{contents}}</p>
    </div>
    <div class="replies" v-if="replies.length">
      <h3 class="h3">댓글</h3>
      <ul class="list-group">
        <li class="list-group-item" v-for="reply in replies" :key="reply.no">
          <div class="row px-3 mb-2 justify-content-between">
            <p class="email">{{reply.email}}</p>
            <p class="date">{{reply.updated_at}}</p>
          </div>
          <p>{{reply.contents}}</p>
        </li>
      </ul>
    </div>
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
<style lang="less" scoped>
  .ie9 {
    .detail {
      .email, .date {
        display: inline-block;
      }
      .email {
        padding-right: 10px;
        border-right: 1px solid rgba(0,0,0,.125);
      }
      .date {
        padding-left: 10px;
      }
    }
  }
</style>
