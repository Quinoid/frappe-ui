<template>
  <div class="relative flex items-center">
    <div
      :class="[
        'absolute inset-y-0 left-0 flex items-center',
        textColor,
        prefixClasses,
      ]"
      v-if="$slots.prefix"
    >
      <slot name="prefix"> </slot>
    </div>
    <div
      v-if="placeholder"
      v-show="!modelValue"
      class="pointer-events-none absolute text-gray-500"
      :class="[fontSizeClasses, paddingClasses]"
    >
      {{ placeholder }}
    </div>
    <select
      :class="selectClasses"
      :disabled="disabled"
      :id="id"
      :value="modelValue"
      @change="handleChange"
      v-bind="attrs"
    >
      <option
        v-for="option in selectOptions"
        :key="option.value"
        :value="option.value"
        :disabled="option.disabled || false"
        :selected="modelValue === option.value"
      >
        {{ option.label }}
      </option>
    </select>
  </div>
</template>

<script setup lang="ts">
import { computed, useSlots, useAttrs } from 'vue'

defineOptions({
  inheritAttrs: false,
})

type SelectOption =
  | string
  | {
      label: string
      value: string
      disabled?: boolean
    }

interface SelectProps {
  size?: 'sm' | 'md' | 'lg'
  variant?: 'subtle' | 'outline' | 'ghost'
  placeholder?: string
  disabled?: boolean
  id?: string
  modelValue?: string | number
  options?: SelectOption[]
}

const props = withDefaults(defineProps<SelectProps>(), {
  size: 'sm',
  variant: 'subtle',
})

const emit = defineEmits(['update:modelValue'])
const slots = useSlots()
const attrs = useAttrs()

function handleChange(e: Event) {
  emit('update:modelValue', (e.target as HTMLInputElement).value)
}

const selectOptions = computed(() => {
  return (
    props.options
      ?.map((option) => {
        if (typeof option === 'string') {
          return {
            label: option,
            value: option,
          }
        }
        return option
      })
      .filter(Boolean) || []
  )
})

const textColor = computed(() => {
  return props.disabled ? 'text-gray-500' : 'text-gray-800'
})

const fontSizeClasses = computed(() => {
  return {
    sm: 'text-base',
    md: 'text-base',
    lg: 'text-lg',
    xl: 'text-xl',
  }[props.size]
})

const paddingClasses = computed(() => {
  return {
    sm: 'px-2',
    md: 'px-2.5',
    lg: 'px-3',
    xl: 'px-3',
  }[props.size]
})

const selectClasses = computed(() => {
  let sizeClasses = {
    sm: 'rounded h-7',
    md: 'rounded h-8',
    lg: 'rounded-md h-10',
    xl: 'rounded-md h-10',
  }[props.size]

  let variant = props.disabled ? 'disabled' : props.variant
  let variantClasses = {
    subtle:
      'border border-gray-100 bg-gray-100 hover:border-gray-200 hover:bg-gray-200 focus:border-gray-500 focus:ring-0 focus-visible:ring-2 focus-visible:ring-gray-400',
    outline:
      'border border-gray-300 bg-white hover:border-gray-400 focus:border-gray-500 focus:ring-0 focus-visible:ring-2 focus-visible:ring-gray-400',
    ghost:
      'bg-transparent border-transparent hover:bg-gray-200 focus:bg-gray-200 focus:border-gray-500 focus:ring-0 focus-visible:ring-2 focus-visible:ring-gray-400',
    disabled: [
      'border',
      props.variant !== 'ghost' ? 'bg-gray-50' : '',
      props.variant === 'outline' ? 'border-gray-300' : 'border-transparent',
    ],
  }[variant]

  return [
    sizeClasses,
    fontSizeClasses.value,
    paddingClasses.value,
    variantClasses,
    textColor.value,
    'transition-colors w-full py-0 bg-white',
  ]
})

let prefixClasses = computed(() => {
  return {
    sm: 'pl-2',
    md: 'pl-2.5',
    lg: 'pl-3',
    xl: 'pl-3',
  }[props.size]
})
</script>
