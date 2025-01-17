<template>
  <el-form
    @submit.prevent
    ref="ruleFormRef"
    class="mb-24"
    label-width="auto"
    :model="form_data"
    v-bind="$attrs"
  >
    <el-form-item label="显示名称" :required="true" prop="label" :rules="rules.label">
      <el-input v-model="form_data.label" placeholder="请输入显示名称" />
    </el-form-item>
    <el-form-item label="参数" :required="true" prop="field" :rules="rules.field">
      <el-input v-model="form_data.field" placeholder="请输入参数" />
    </el-form-item>
    <el-form-item label="参数提示说明">
      <el-input v-model="form_data.tooltip" placeholder="请输入参数提示说明" />
    </el-form-item>
    <el-form-item label="是否必填" :required="true" prop="required" :rules="rules.required">
      <el-switch v-model="form_data.required" />
    </el-form-item>
    <el-form-item label="组件类型" :required="true" prop="input_type" :rules="rules.input_type">
      <el-select v-model="form_data.input_type" placeholder="请选择组件类型">
        <el-option
          v-for="input_type in input_type_list"
          :key="input_type.value"
          :label="input_type.label"
          :value="input_type.value"
        />
      </el-select>
    </el-form-item>
    <component
      v-if="form_data.input_type"
      ref="componentFormRef"
      v-model="form_data"
      :is="form_data.input_type"
    ></component>
  </el-form>
</template>
<script setup lang="ts">
import { onMounted, ref } from 'vue'
import type { FormInstance } from 'element-plus'

const props = defineProps<{
  modelValue: any
}>()
const emit = defineEmits(['update:modelValue'])

const ruleFormRef = ref<FormInstance>()
const input_type_list = [
  { label: '文本框', value: 'TextInputConstructor' },
  { label: '滑块', value: 'SliderConstructor' },
  { label: '开关', value: 'SwitchInputConstructor' },
  { label: '单选框', value: 'SingleSelectConstructor' }
]
const componentFormRef = ref<any>()
const form_data = ref<any>({
  label: '',
  field: '',
  tooltip: '',
  required: false,
  input_type: ''
})
const rules = {
  label: [{ required: true, message: '参数 为必填属性' }],
  field: [{ required: true, message: '显示名称 为必填属性' }],
  required: [{ required: true, message: '是否必填 为必填属性' }],
  input_type: [{ required: true, message: '组建类型 为必填属性' }]
}
const getData = () => {
  let label: string | any = form_data.value.label
  if (form_data.value.tooltip) {
    label = {
      input_type: 'TooltipLabel',
      label: form_data.value.label,
      attrs: { tooltip: form_data.value.tooltip },
      props_info: {}
    }
  }
  return {
    label: label,
    required: form_data.value.required,
    field: form_data.value.field,
    default_value: form_data.value.default_value,
    ...componentFormRef.value.getData()
  }
}

const validate = () => {
  if (ruleFormRef.value) {
    return ruleFormRef.value?.validate()
  }
  return Promise.resolve()
}

onMounted(() => {
  if (props.modelValue) {
    const data = props.modelValue
    // console.log(data)
    form_data.value = data
    // 处理option
    form_data.value.input_type = data.input_type + 'Constructor'
    if (data.label && data.label.input_type === 'TooltipLabel') {
      form_data.value.tooltip = data.label.attrs.tooltip
      form_data.value.label = data.label.label
    } else {
      form_data.value.label = data.label
    }
  }
})

defineExpose({ getData, validate })
</script>
<style lang="scss"></style>
