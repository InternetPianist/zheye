<template>
  <div class="row">
    <div class="col-4 mb-4" v-for="column in columnList" :key="column._id">
      <div class="card h-100 shadow-sm">
        <div class="card-body text-center">
          <img :src="column.avatar && column.avatar.url" :alt="column.title" class="rounded-circle border border-light my-3">
          <h5 class="card-title">{{column.title}}</h5>
          <p class="card-text text-left">{{column.description}}</p>
          <router-link :to="{name: 'column',params: {id: column._id}}" class="btn btn-outline-primary">进入专栏</router-link>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { addColumnAvatar } from '@/helper'
import { defineComponent, PropType, computed } from 'vue'
import { ColumnProps } from '../store'

export default defineComponent({
  name: 'ColumnList',
  props: {
    list: {
      // PropType 强制转换构造函数
      type: Array as PropType<ColumnProps[]>,
      required: true
    }
  },
  setup (props) {
    const columnList = computed(() => {
      return props.list.map(column => {
        addColumnAvatar(column, 50, 50)
        // if (!column.avatar) {
        //   column.avatar = {
        //     url: require('@/assets/column.jpg')
        //   }
        // } else {
        //   column.avatar.url = column.avatar.url + '?x-oss-process=image/resize,m_fixed,h_50,w_50'
        // }
        return column
      })
    })
    return {
      columnList
    }
  }
})
</script>

<style scoped>
.card-body img{
  width: 50px;
  height: 50px;
}
</style>
