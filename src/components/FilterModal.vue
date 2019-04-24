<template>
  <div>
    <div class="select-backround"></div>
    <div class="select-container">
      <p>필터</p>
      <ul class="select-list">
        <li v-for="category in categoryList" :key="category.no">
          <input
            type="checkbox"
            ref="categoryCehck"
            :name="'category'+ category.no"
            :value="category.no"
            :checked="checkedCategory.indexOf(category.no) > -1">
            {{ category.name }}
        </li>
      </ul>
      <button @click="saveCategory">저장</button>
    </div>
  </div>
</template>
<script>
export default {
  name: 'FilterModal',
  props: {
    categoryList: Array,
    checkedCategory: String,
    showFilterModal: Boolean
  },
  methods: {
    saveCategory () {
      this.$emit('update:checkedCategory',
        this.$refs.categoryCehck.filter(input => input.checked).map(input => input.value).join(',')
      )
      this.$emit('update:showFilterModal', false)
    }
  }
}
</script>
