<template>
  <teleport to="#modal">
    <div class="modal d-block" tabindex="-1" v-if="visible">
      <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">{{title}}</h5>
              <button type="button" class="btn-close" @click="onClose" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <div class="modal-body">
              <slot></slot>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-bs-dismiss="modal" @click="onClose">取消</button>
              <button type="button" class="btn btn-primary" @click="onConfirm">确认</button>
            </div>
          </div>
      </div>
    </div>
  </teleport>
</template>

<script lang="ts">
import useDOMCreate from '@/hooks/useDOMCreate'
import { defineComponent, onMounted } from 'vue'

export default defineComponent({
  props: {
    title: String,
    visible: {
      type: Boolean,
      default: false
    }
  },
  emits: ['modal-on-close', 'modal-on-confirm'],
  setup (props, context) {
    onMounted(() => {
      console.log(props)
    })
    useDOMCreate('modal')
    const onClose = () => {
      context.emit('modal-on-close')
    }
    const onConfirm = () => {
      context.emit('modal-on-confirm')
    }
    return {
      onClose,
      onConfirm
    }
  }
})
</script>

<style>

</style>
