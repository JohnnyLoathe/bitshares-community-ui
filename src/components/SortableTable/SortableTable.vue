<template>
  <div class="sortable-table">

    <div
      :style="headerStyle"
      class="sortable-table__header"
    >
      <SortableHeaderItem
        v-for="(header, index) in headers"
        :key="index"
        :title="header.title"
        :sort="sort.field === header.field && sort.type || ''"
        :align="header.align"
        @click.native="toggleSort(header.field)"
      />
    </div>

    <div class="sortable-table__body">
      <slot :sorted-items="sortedItems"/>
    </div>

  </div>
</template>

<script>
import SortableHeaderItem from '@/components/SortableHeaderItem'
import orderBy from 'lodash/orderBy'

export default {
  components: { SortableHeaderItem },
  props: {
    headers: {
      type: Array,
      required: true
    },
    defaultSort: {
      type: Object,
      required: false,
      default: () => ({
        field: '',
        type: 'desc'
      })
    },
    items: {
      required: true,
      type: Array
    }
  },
  data() {
    return {
      sort: {
        field: '',
        type: 'desc'
      }
    }
  },
  computed: {
    sortedItems() {
      if (!this.sort.field) return this.items
      return orderBy(this.items, this.sort.field, this.sort.type)
    },
    headerStyle() {
      return {
        'grid-template-columns': `repeat(${this.headers.length}, 1fr)`
      }
    }
  },
  created() {
    this.sort = this.defaultSort
  },
  methods: {
    toggleSort(field) {
      if (this.sort.field === field) {
        this.sort.type = this.sort.type === 'asc' ? 'desc' : 'asc'
        return
      }
      this.sort.field = field
      this.sort.type = 'desc'
    }
  }
}
</script>

<style lang="scss">
  .sortable-table__header {
    color: config('colors.text-primary');
    padding-bottom: config('padding.2');
    display: grid;
  }
</style>