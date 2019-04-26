<template>
  <div class="modal" id="filter-modal" tabindex="-1" role="dialog" aria-label="카테고리 필터" aria-hidden="true">
    <div class="modal-dialog" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <p class="modal-title">필터</p>
          <button type="button" class="close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div class="modal-body">
          <ul class="row justify-content-around">
            <li v-for="category in categoryList" :key="category.no">
              <input
                type="checkbox"
                ref="categoryCehck"
                :name="'category'+ category.no"
                :value="category.no">
                {{ category.name }}
            </li>
          </ul>
          <p class="alert alert-warning mt-2" role="alert" v-show="showAlert">
            필터를 선택해주세요.
          </p>
        </div>
        <div class="modal-footer row ml-2 mr-2">
          <button type="button" class="btn btn-primary offset-md-10" @click="saveCategory">저장</button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import $ from 'jquery'

export default {
  name: 'FilterModal',
  props: {
    categoryList: Array,
    checkedCategory: String
  },
  data () {
    return {
      showAlert: false
    }
  },
  mounted () {
    $('#filter-modal').on('show.bs.modal', this.checkCategory)
  },
  methods: {
    saveCategory () {
      if (!this.$refs.categoryCehck.filter(input => input.checked).length) {
        this.toggleAlert(true)
        return false
      } else {
        this.$emit('update:checkedCategory',
          this.$refs.categoryCehck.filter(input => input.checked).map(input => input.value).join(',')
        )
        this.toggleAlert(false)
        $('#filter-modal').modal('hide')
      }
    },
    toggleAlert (bool) {
      this.showAlert = bool
    },
    // 체크박스 동적 연결, 사용자가 저장하지 않고 닫은 경우에 대한 대응
    checkCategory () {
      this.toggleAlert(false)
      this.$refs.categoryCehck.forEach(input => {
        input.checked = this.checkedCategory.indexOf(input.value) > -1
      })
    }
  }
}
</script>
<style lang="less" scoped>
  .ie9 {
    .modal {
      .close {
        margin-top: -40px;
      }
      .modal-body {
        ul {
          text-align: center;
          li {
            display: inline-block;
            margin-right: 50px;
          }
          li:last-child {
            margin-right: 0;
          }
        }
      }
    }
  }
</style>
