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
        <ul class="modal-body">
          <li v-for="category in categoryList" :key="category.no">
            <input
              type="checkbox"
              ref="categoryCehck"
              :name="'category'+ category.no"
              :value="category.no">
              {{ category.name }}
          </li>
        </ul>
        <div class="modal-footer">
          <button type="button" class="btn btn-primary" @click="saveCategory">저장</button>
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
  mounted () {
    $('#filter-modal').on('show.bs.modal', this.checkCategory)
  },
  methods: {
    saveCategory () {
      this.$emit('update:checkedCategory',
        this.$refs.categoryCehck.filter(input => input.checked).map(input => input.value).join(',')
      )
      $('#filter-modal').modal('hide')
    },
    // 체크박스 동적 연결, 사용자가 저장하지 않고 닫은 경우에 대한 대응
    checkCategory () {
      this.$refs.categoryCehck.forEach(input => {
        input.checked = this.checkedCategory.indexOf(input.value) > -1
      })
    }
  }
}
</script>
