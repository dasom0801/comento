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
      <li v-for="item in printList" :key="item.email ? 'content' + item.no : 'ad' + item.no">
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
  mounted () {
    window.scrollTo(0, 0)
    window.addEventListener('scroll', this.handleScroll)
  },
  destroyed () {
    window.removeEventListener('scroll', this.handleScroll)
  },
  data () {
    return {
      page: 0,
      adsPage: 0,
      order: 'asc',
      showFilterModal: false,
      isLoading: false,
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
    // 카테고리 리스트 불러오기
    fetchCategory () {
      axios.get('http://comento.cafe24.com/category.php')
        .then(function ({data}) {
          this.categoryList = data.list
        }.bind(this))
        .catch(error => {
          console.log(error)
        })
    },
    // 컨텐츠 리스트 불러오기
    fetchContentList () {
      this.page++
      axios.get(`http://comento.cafe24.com/request.php?page=${this.page}&ord=${this.order}&category=${this.checkedCategory}`)
        .then(function ({data}) {
          this.contentList = data.list
          // 콘텐츠리스트 40개당 광고리스트 호출 1회 필요
          this.page % 4 === 1 ? this.fetchAdsList() : this.makeList()
        }.bind(this))
        .catch(error => {
          console.log(error)
        })
    },
    // 광고 리스트 불러오기
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
    // 출력하기 위해 콘텐츠와 광고를 합친 배열 만들기
    makeList () {
      const {contentList, adsList, page} = this
      while (contentList.length > 0) {
        // 페이지가 홀수일 때는 마지막에 콘텐츠가 2개 남고, 짝수일 때는 처음 2개 이후에 광고 아이템이 추가되어야 한다.
        if (page % 2) {
          this.printList =
          contentList.length < 4
            ? this.printList.concat(contentList.splice(0, 2))
            : this.printList.concat(contentList.splice(0, 4)).concat(adsList.splice(0, 1))
        } else {
          this.printList =
          contentList.length % 4
            ? this.printList.concat(contentList.splice(0, 2)).concat(adsList.splice(0, 1))
            : this.printList.concat(contentList.splice(0, 4)).concat(adsList.splice(0, 1))
        }
      }
      this.changeLoadingStatus(false)
    },
    // 설정이 바뀔 때 데이터 초기화
    resetData () {
      this.page = 0
      this.adsPage = 0
      this.contentList = []
      this.adsList = []
      this.printList = []
    },
    // 오름차순, 내림차순 정렬 변경
    toggleSort (order) {
      if (this.order === order) return false
      this.order = this.order === 'asc' ? 'desc' : 'asc'
      this.resetData()
      this.fetchContentList()
    },
    // 필터 모달 토글
    toggleFilterModal () {
      this.showFilterModal = !this.showFilterModal
    },
    // 무한 스크롤 이벤트
    handleScroll () {
      const scrollHeight = Math.max(document.documentElement.scrollHeight, document.body.scrollHeight)
      const scrollTop = Math.max(document.documentElement.scrollTop, document.body.scrollTop)
      const clientHeight = document.documentElement.clientHeight
      if (scrollTop + clientHeight >= scrollHeight - 300) {
        // 데이터를 불러오는 중에 데이터 요청하지 않음
        if (!this.isLoading) {
          this.changeLoadingStatus(true)
          this.fetchContentList()
        } else {
          return false
        }
      }
    },
    // 로딩 표시를 위한 값
    changeLoadingStatus (bool) {
      this.isLoading = bool
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
