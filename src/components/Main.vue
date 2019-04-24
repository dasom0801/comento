<template>
  <div class="main">
    <button class="filter" @click="toggleFilterModal">필터</button>
    <filter-modal
      v-show="showFilterModal"
      :categoryList="categoryList"
      :checkedCategory.sync="checkedCategory"
      :showFilterModal.sync="showFilterModal"
    >
    </filter-modal>
    <ul class="sort">
      <li :class="this.order==='asc'? 'is-active' : ''">
        <button @click="toggleSort('asc')">오름차순</button>
      </li>
      <li :class="this.order==='desc'? 'is-active' : ''">
        <button @click="toggleSort('desc')">내림차순</button>
      </li>
    </ul>
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
          :title="item.title">
        </ad-item>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios'
import contentItem from './ContentItem'
import adItem from './AdItem'
import FilterModal from './FilterModal'

export default {
  name: 'Main',
  components: {
    contentItem, adItem, FilterModal
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
      showFilterModal: false,
      categoryList: [],
      checkedCategory: '1,2,3',
      contentList: [],
      adsList: [],
      printList: []
    }
  },
  watch: {
    checkedCategory: function () {
      this.resetData()
      this.fetchContentList()
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
    fetchContentList () {
      this.page++
      axios.get(`http://comento.cafe24.com/request.php?page=${this.page}&ord=${this.order}&category=${this.checkedCategory}`)
        .then(function ({data}) {
          this.contentList = data.list
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
          this.adsList = data.list
          this.makeList()
        }.bind(this))
        .catch(error => {
          console.log(error)
        })
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
    },
    resetData () {
      this.page = 0
      this.adsPage = 0
      this.contentList = []
      this.adsList = []
      this.printList = []
    },
    toggleSort (order) {
      if (this.order === order) return false
      this.order = this.order === 'asc' ? 'desc' : 'asc'
      this.resetData()
      this.fetchContentList()
    },
    toggleFilterModal () {
      this.showFilterModal = !this.showFilterModal
    }
  }
}
</script>
<style lang="less" scoped>
  .sort {
    li.is-active button {
      color: red;
    }
  }
</style>
