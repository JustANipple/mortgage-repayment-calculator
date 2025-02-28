<script setup>
import BaseRadio from "./BaseRadio.vue";

const props = defineProps({
    options: {
        type: Array,
        required: true,
    },
    name: {
        type: String,
        required: true,
    },
    modelValue: {
        type: [String, Number],
        required: true,
    },
    vertical: {
        type: Boolean,
        default: false,
    },
    error: {
        type: String,
        default: "",
    },
});
</script>

<template>
    <component
        v-for="option in options"
        :key="option.value"
        :is="vertical ? 'div' : 'span'"
        :class="{
            horizontal: !vertical,
            selected: modelValue === option.value,
        }"
        class="radio-group"
    >
        <BaseRadio
            :label="option.label"
            :value="option.value"
            :modelValue="modelValue"
            :name="name"
            @update:modelValue="$emit('update:modelValue', $event)"
        />
    </component>
    <p v-if="error" class="error">
        {{ error }}
    </p>
</template>

<style scoped>
.horizontal {
    margin-right: 20px;
}

.radio-group {
    border: 1px solid var(--slate700);
    border-radius: 4px;
    padding: 10.5px 17px;
    display: flex;
    align-items: center;
    gap: 18px;
}

.selected {
    border-color: var(--primary-color);
    background-color: rgba(var(--primary-color-values), 0.2);
}

.error {
    color: var(--error-color);
    font-size: 14px;
}
</style>
