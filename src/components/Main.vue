<template>
  <div class="main">
    <ul class="contents">
      <li v-for="item in printList" :key="item.no">
        <content-item v-if="item.email"
          :categoryList="categoryList"
          :category="item.category_no"
          :contents="item.contents"
          :email="item.email"
          :no="item.no"
          :title="item.title"
          :update="item.updated_at">
        </content-item>
        <ad-item v-else
          :contents="item.contents"
          :img="item.img"
          :title="item.title"
        ></ad-item>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios'
import contentItem from './ContentItem'
import adItem from './AdItem'
export default {
  name: 'Main',
  components: {
    contentItem, adItem
  },
  created () {
    this.fetchContentList({order: 'asc'})
    this.fetchCategory()
  },
  data () {
    return {
      page: 0,
      adsPage: 0,
      order: 'asc',
      categoryList: [],
      checkedCategory: '1,2,3',
      contentList: [],
      adsList: [],
      printList: []
    }
  },
  methods: {
    fetchCategory () {
      axios.get('http://comento.cafe24.com/category.php')
        .then(function ({data}) {
          this.categoryList = data.list
        }.bind(this))
        .catch(error => {
          console.log(error)
        })
    },
    fetchContentList ({order}) {
      this.page++
      this.order = order
      axios.get(`http://comento.cafe24.com/request.php?page=${this.page}&ord='${this.order}'&catecory=${this.checkedCategory}`)
        .then(function ({data}) {
          this.contentList = this.contentList.concat(data.list)
          this.page % 4 === 1 ? this.fetchAdsList() : this.makeList()
        }.bind(this))
        .catch(error => {
          console.log(error)
        })
    },
    fetchAdsList () {
      this.adsPage++
      axios.get(`http://comento.cafe24.com/ads.php?page=${this.adsPage}`)
        .then(function ({data}) {
          this.adsList = this.adsList.concat(data.list)
          this.makeList()
        }.bind(this))
    },
    makeList () {
      // 짝수 페이지에서 list에 2개 먼저 붙여넣기 필요 > 무한 스크롤하면서 추가하기
      const {contentList, adsList} = this
      // let listCount = (page * 10) - 10
      while (contentList.length > 0) {
        this.printList =
        contentList.length < 4
          ? this.printList.concat(contentList.splice(0, 2))
          : this.printList.concat(contentList.splice(0, 4)).concat(adsList.splice(0, 1))
      }
    }
  }
}
</script>
<style scoped>
</style>
