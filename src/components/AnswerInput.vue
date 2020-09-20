<script lang="ts">
import { defineComponent } from 'vue'
import { useVModel } from '../hooks/use-vModel'

export default defineComponent({
  name: 'AnswerInput',
  props: {
    name: String,
    modelValue: String,
    required: {
      type: Boolean,
      default: true
    }
  },
  setup(props, { emit }) {
    return {
      value: useVModel(props, emit)
    }
  }
})
</script>

<template>
  <label :for="name" class="flex flex-col mx-2 relative">
    <input
      type="text"
      :name="name"
      :id="name"
      class="appearance-none border-b-2 border-gray-400 rounded-none text-center focus:outline-none focus:border-gray-800"
      v-model="value"
      :required="required"
    >
  </label>
</template>