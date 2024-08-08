<template>
    <div class="flex flex-col gap-1">
        <span class="text-sm text-black font-semibold">{{ props.text }}
            <span class="text-red-500" v-show="props.required"> *</span>
        </span>
        
        <div class="flex gap-2" v-for="option in props.options" :key="option">
            <input 
                type="radio" 
                class="rounded-md p-1.5 border-gray-300 outline-slate-500" 
                :name="props.name" 
                :value="option"
                :checked="modelValue === option" 
                @change="handleSelection(option)" 
                :required="props.required && !modelValue"
            >
            <label>{{ option }}</label>
        </div>
        
        <div class="flex gap-2" v-show="other">
            <input 
                type="radio" 
                class="rounded-md p-1.5 border-gray-300 outline-slate-500" 
                :name="props.name" 
                :checked="isOtherSelected"
                @change="handleOtherSelection"
            >
            <label>Otro</label>
        </div>

        <input 
            v-show="showOther" 
            type="text" 
            class="rounded-md shadow-sm p-1.5 border-gray-300 outline-slate-500"
            :placeholder="props.placeholder" 
            v-model="otherValue"
            @input="handleOtherInput" 
            :required="props.required && showOther && !modelValue"
        >
    </div>
</template>

<script setup>
import { defineProps, defineEmits, ref, watch } from 'vue';

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
    },
    placeholder: {
        type: String,
    },
    options: {
        type: Array,
        required: true,
    },
    other: {
        type: Boolean,
    },
    required: {
        type: Boolean,
    }
});

const showOther = ref(false);
const isOtherSelected = ref(false);
const otherValue = ref('');

const emit = defineEmits(['update:modelValue']);

function handleSelection(option) {
    showOther.value = false;
    isOtherSelected.value = false;
    emit('update:modelValue', option);
}

function handleOtherSelection() {
    showOther.value = true;
    isOtherSelected.value = true;
    emit('update:modelValue', otherValue.value); 
}

function handleOtherInput(event) {
    otherValue.value = event.target.value;
    emit('update:modelValue', otherValue.value);
}

// Si el usuario cambia el modelValue externamente, verifica si es "Otro" u otra opción
watch(() => props.modelValue, (newValue) => {
    if (newValue === otherValue.value) {
        showOther.value = true;
        isOtherSelected.value = true;
    } else {
        showOther.value = false;
        isOtherSelected.value = false;
    }
});
</script>
