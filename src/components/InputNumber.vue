<template>
    <div class="flex flex-col gap-1">
        <label :for="name" class="text-sm text-black font-semibold">{{ props.text }}<span class="text-red-500" v-show="props.required"> *</span></label>
        <input type="number" class="rounded-md shadow-sm p-1.5 border border-gray-300 outline-slate-500"
            :placeholder="props.placeholder" :name="name" :value="modelValue"
            @input="$emit('update:modelValue', $event.target.value)" :required="props.required">
    </div>
</template>
<script setup>
import { defineProps, defineEmits } from 'vue';
const props = defineProps({
    modelValue: {
        type: String,
    },
    name: {
        type: String,
        required: true,
    },
    text: {
        type: String,
        required: true,
    }
    ,
    placeholder: {
        type: String,
    }
    ,
    required:{
        type: Boolean,
    }
});

defineEmits(['update:modelValue']);
</script>
