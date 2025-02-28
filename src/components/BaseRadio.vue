<script setup>
const props = defineProps({
    label: {
        type: String,
        default: "",
    },
    modelValue: {
        type: [String, Number],
        default: "",
    },
    value: {
        type: [String, Number],
        required: true,
    },
});
</script>

<template>
    <input
        type="radio"
        :checked="modelValue === value"
        :value="value"
        v-bind="$attrs"
        @change="$emit('update:modelValue', value)"
        class="field"
    />
    <label v-if="label">{{ label }}</label>
</template>

<style scoped>
input[type="radio"] {
    -webkit-appearance: none;
    appearance: none;
    background-color: transparent;
    margin: 0;
    margin-top: 2px;

    font: inherit;
    color: var(--slate700);
    width: 1.25em;
    height: 1.25em;
    border: 0.15em solid var(--slate700);
    border-radius: 50%;
    transform: translateY(-0.075em);

    display: grid;
    place-content: center;
}

input[type="radio"]:checked {
    color: var(--primary-color);
    border-color: var(--primary-color);
}

input[type="radio"]::before {
    content: "";
    width: 0.65em;
    height: 0.65em;
    border-radius: 50%;
    transform: scale(0);
    transition: 120ms all ease-in-out;
    background-color: var(--primary-color);
}

input[type="radio"]:checked::before {
    transform: scale(1);
}

label {
    color: var(--slate900);
    font-weight: var(--fw-bold);
    font-size: 18px;
}
</style>
