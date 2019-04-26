<template>
  <div class="main container">
    <div class="input-group col-lg-6 col-md-8 mx-auto mb-3">
      <input type="text" v-model="searchKeyword" @keyup="inputKeyword($event)" class="form-control" placeholder="검색어를 입력해주세요." aria-label="검색어를 입력해주세요.">
    </div>
    <p class="alert alert-primary col-lg-6 col-md-8 mx-auto" role="alert" v-show="showSearchAlert">
      검색어를 입력해주세요.
    </p>
    <div class="select-group row justify-content-between mb-3">
      <div class="filter-group row">
        <button class="filter mr-2 btn btn-primary" data-toggle="modal" data-target="#filter-modal">필터</button>
        <p class="py-2"> {{checkedCategoryName}}</p>
        <filter-modal
          :categoryList="categoryList"
          :checkedCategory.sync="checkedCategory">
        </filter-modal>
      </div>
      <ul class="sort row justify-content-end">
        <li :class="this.order==='asc'? 'is-active' : ''">
          <button @click="toggleSort('asc')">오름차순</button>
        </li>
        <li :class="this.order==='desc'? 'is-active' : ''">
          <button @click="toggleSort('desc')">내림차순</button>
        </li>
      </ul>
    </div>
    <ul class="contents">
      <li class="card mb-4 pl-3 pr-3" v-for="(item, index) in filteredList" :key="item.email ? 'content' + item.no : 'ad' + index">
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
    <div class="loading" v-show="isLoading">
      <div class="spinner-border text-light" role="status">
        <span class="sr-only">Loading...</span>
      </div>
    </div>
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
    !localStorage.getItem('category') && this.setCategory('1,2,3')
    !localStorage.getItem('order') && this.setOrder('asc')
    this.getCategory()
    this.getOrder()
    this.fetchContentList()
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
      order: 'asc',
      searchKeyword: '',
      isLoading: false,
      categoryList: [],
      checkedCategory: '1,2,3',
      contentList: [],
      adsList: [],
      printList: []
    }
  },
  computed: {
    checkedCategoryName () {
      return this.categoryList.filter(category => this.checkedCategory.indexOf(category.no) > -1)
        .map(category => category.name).join(', ')
    },
    filteredList () {
      return this.searchKeyword.trim().length
        ? this.printList.filter(item => item.email && (item.title.toLowerCase().indexOf(this.searchKeyword.toLowerCase()) > -1 || item.contents.toLowerCase().indexOf(this.searchKeyword.toLowerCase()) > -1))
        : this.printList
    },
    showSearchAlert () {
      return this.searchKeyword !== '' && !this.searchKeyword.trim().length
    }
  },
  watch: {
    checkedCategory: function () {
      this.resetData()
      this.fetchContentList()
      this.setCategory(this.checkedCategory)
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
          this.fetchAdsList()
        }.bind(this))
        .catch(error => {
          console.log(error)
        })
    },
    // 광고 리스트 불러오기
    fetchAdsList () {
      // 광고는 랜덤으로 호출
      const limit = this.page % 2 ? 2 : 3
      const adsPage = limit === 2 ? Math.floor(Math.random() * 15) + 1 : Math.floor(Math.random() * 10) + 1
      axios.get(`http://comento.cafe24.com/ads.php?page=${adsPage}&limit=${limit}`)
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
      setTimeout(() => {
        this.changeLoadingStatus(false)
      }, 1000)
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
      this.setOrder(order)
      this.resetData()
      this.fetchContentList()
    },
    // 무한 스크롤 이벤트
    handleScroll () {
      const scrollHeight = Math.max(document.documentElement.scrollHeight, document.body.scrollHeight)
      const scrollTop = Math.max(document.documentElement.scrollTop, document.body.scrollTop)
      const clientHeight = document.documentElement.clientHeight
      if (scrollTop + clientHeight >= scrollHeight - 200) {
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
    },
    // 키워드 검색
    inputKeyword (event) {
      this.searchKeyword = event.target.value
    },
    // 필터와 정렬 localstorage에서 관리
    // localstorage를 지원하지 않는 브라우저는 data사용
    setCategory (category) {
      if (localStorage) {
        localStorage.setItem('category', category)
      } else {
        this.checkedCategory = category
      }
    },
    setOrder (order) {
      if (localStorage) {
        localStorage.setItem('order', order)
      }
      this.getOrder()
    },
    getCategory () {
      this.checkedCategory = localStorage ? localStorage.getItem('category') : '1,2,3'
    },
    getOrder () {
      this.order = localStorage ? localStorage.getItem('order') : 'asc'
    }
  }
}
</script>
<style lang="less" scoped>
  .main {
    padding: 15px;
    .row {
      padding: 0 15px;
    }
    .input-group {
      .form-control {
        width: 100%;
      }
    }
    .sort {
      li button {
        background: none;
        border: none;
      }
      li.is-active button {
        color: red;
      }
    }
    .loading {
      position: fixed;
      z-index: 10000;
      left: 0;
      top: 0;
      width: 100vw;
      height: 100vh;
      background: rgba(0,0,0,0.4);
      .spinner-border{
        position: absolute;
        left: 50%;
        top: 50%;
        margin-left: -1.5rem;
        margin-top: -1.5rem;
        width: 3rem;
        height: 3rem;
      }
    }
  }
  .ie9 {
    .main {
      .select-group {
        .filter-group {
          float: left;
          p {
            display: inline-block;
          }
        }
        .sort {
          float: right;
          li {
            display: inline-block;
          }
        }
      }
      .select-group:after{
        content: '';
        display: block;
        clear: both
      }
    }
  }
</style>
