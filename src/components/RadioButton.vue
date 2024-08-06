<template>
    <div class="flex flex-col gap-1">
        <span class="text-sm text-black font-semibold">{{ props.text }}</span>
        <div class="flex gap-2" v-for="option in props.options" :key="option">
            <input type="radio" class="rounded-md p-1.5 border-gray-300 outline-slate-500" :name="name" :value="option"
                :checked="modelValue === option" @change="$emit('update:modelValue', option)">
            <label :for="name">{{ option }}</label>
        </div>
        <div class="flex gap-2" v-show="props.other">
            <input type="radio" class="rounded-md p-1.5 border-gray-300 outline-slate-500" name="otro"
                v-model="showOther">
            <label for="otro">Otro</label>
        </div>
        <input v-show="showOther" type="text" class="rounded-md shadow-sm p-1.5 border-gray-300 outline-slate-500"
            :placeholder="props.placeholder" :value="modelValue"
            @input="$emit('update:modelValue', $event.target.value)">
    </div>
</template>
<script setup>
import { defineProps, defineEmits, ref } from 'vue';
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
    },
    options: {
        type: Array,
        required: true,
    },
    other: {
        type: Boolean,
    }
});
const showOther = ref(false);
defineEmits(['update:modelValue']);
</script>
