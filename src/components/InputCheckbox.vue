<template>
    <div class="flex flex-col gap-1">
        <span class="text-sm text-black font-semibold">{{ text }}<span class="text-red-500" v-show="props.required"> *</span></span>
        <div class="flex flex-wrap gap-4">
            <!-- Primera columna -->
            <div class="flex flex-col">
                <div class="flex gap-2" v-for="option in firstColumnOptions" :key="option">
                    <input 
                        type="checkbox" 
                        class="rounded-md p-1.5 border-gray-300 outline-slate-500" 
                        :name="option"
                        :value="option" 
                        :checked="modelValue.includes(option)" 
                        @change="toggleSelection(option)"
                        :required="!modelValue.length && props.required"
                    />
                    <label :for="option">{{ option }}</label>
                </div>
            </div>

            <!-- Segunda columna -->
            <div class="flex flex-col">
                <div class="flex gap-2" v-for="option in secondColumnOptions" :key="option">
                    <input 
                        type="checkbox" 
                        class="rounded-md p-1.5 border-gray-300 outline-slate-500" 
                        :name="option"
                        :value="option" 
                        :checked="modelValue.includes(option)" 
                        @change="toggleSelection(option)" 
                        :required="!modelValue.length && props.required"
                    />
                    <label :for="option">{{ option }}</label>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { defineProps, defineEmits, computed } from 'vue';

const props = defineProps({
    modelValue: {
        type: Array,
        default: () => []
    },
    name: {
        type: String,
        required: true,
    },
    text: {
        type: String,
        required: true,
    },
    options: {
        type: Array,
        required: true,
    },
    required:{
        type: Boolean
    }
});

const emit = defineEmits(['update:modelValue']);

const firstColumnOptions = computed(() => {
    return props.options.slice(0, Math.ceil(props.options.length / 2));
});

const secondColumnOptions = computed(() => {
    return props.options.slice(Math.ceil(props.options.length / 2));
});

const toggleSelection = (option) => {
    const selectedOptions = [...props.modelValue];

    if (selectedOptions.includes(option)) {
        // Deselect
        const index = selectedOptions.indexOf(option);
        selectedOptions.splice(index, 1);
    } else {
        // Select
        selectedOptions.push(option);
    }

    emit('update:modelValue', selectedOptions);
};
</script>
