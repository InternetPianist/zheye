<template>
  <div class="validate-input-container pb-3">
    <input
        v-if="tag!=='textarea'"
        class="form-control"
        :class="{'is-invalid ': inputRef.error}"
        @blur="vaildateInput"
        v-model="inputRef.val"
        v-bind="$attrs"/>
    <textarea
      v-else
      class="form-control"
      :class="{'is-invalid ': inputRef.error}"
      @blur="vaildateInput"
      v-model="inputRef.val"
      v-bind="$attrs">
    </textarea>
    <span v-if="inputRef.error" class="invalid-feedback" >{{inputRef.message}}</span>
  </div>
</template>

<script lang="ts">
/*
  1. 自定义验证规则实现
  2. 自定义组件v-model的绑定实现
  3. 绑定到根组件的属性，传入到子元素上
*/
import { computed, defineComponent, onMounted, PropType, reactive, watch } from 'vue'
import { emitter } from './VaildateForm.vue' // 导入监听器
const emailReg = /^[0-9a-zA-z_.-]+[@][0-9a-zA-Z_.-]+([.][0-9a-z]+){1,2}$/

interface RulePorp {
  type: 'required' | 'email' | 'custom';
  message: string;
  vaildator?: () => boolean;
}

export type RulesPorp = RulePorp[]
export type TageType = 'input' | 'textarea'
export default defineComponent({
  name: 'VaildateInput',
  props: {
    rules: {
      type: Array as PropType<RulesPorp>
    },
    modelValue: String,
    tag: {
      type: String as PropType<TageType>,
      default: 'input'
    }
  },
  inheritAttrs: false, // inheritAttrs: false 和 $attrs，你就可以手动决定这些 attribute 会被赋予哪个元素。
  setup (props, context) {
    // console.log(context.attrs) // 在通过v-bind="$attrs" 绑定到根组件传入属性，的子元素上
    const inputRef = reactive({
      val: computed({
        get: () => props.modelValue || '',
        set: val => {
          context.emit('update:modelValue', val)
        }
      }),
      error: false,
      message: ''
    })
    // watch(() => props.modelValue, (newVal) => {
    //   console.log('watch triggered')
    //   inputRef.val = newVal || ''
    // })
    // // 用于实现根组件双向绑定 v-model
    // const updateValue = (e: KeyboardEvent) => {
    //   console.log('update triggered')
    //   // 获取input value值
    //   const targetValue = (e.target as HTMLInputElement).value
    //   // 将input值给 当inputRef便于验证
    //   inputRef.val = targetValue
    //   // 发射更新事件给根标签更新值
    //   context.emit('update:modelValue', targetValue)
    // }
    emitter.on('resetInput', () => {
      inputRef.val = ''
    })
    // 用于验证输入值是否合法
    const vaildateInput = () => {
      if (props.rules) {
        // 只要有一个为false 那么就为false
        const allPassed = props.rules.every(rule => {
          let passed = true
          inputRef.message = rule.message
          switch (rule.type) {
            case 'required': passed = (inputRef.val.trim() !== '')
              break
            case 'email': passed = emailReg.test(inputRef.val)
              break
            case 'custom': passed = rule.vaildator ? rule.vaildator() : true
              break
            default:
              break
          }
          return passed
        })
        inputRef.error = !allPassed
        return allPassed
      }
      return true
    }
    onMounted(() => {
      // 发送事件给监听者
      emitter.emit('form-item-created', vaildateInput)
    })
    return {
      inputRef,
      vaildateInput
    }
  }
})
</script>

<style>

</style>
