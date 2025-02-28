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
    textContent: {
        type: String,
        required: true,
    },
    isLeft: {
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
    <div
        class="grid-container"
        :style="{
            '--content': `'${textContent}'`,
            '--isLeft': isLeft ? 'unset' : 'auto',
            '--leftSpace': isLeft ? '60px' : '16px',
            '--error': error
                ? 'var(--error-color)'
                : 'var(--slate100)',
            '--error-text': error
                ? 'var(--white)'
                : 'var(--slate700)',
        }"
    >
        <label v-if="label" class="label">{{ label }}</label>
        <div class="inputContainer">
            <input
                v-bind="$attrs"
                :value="modelValue"
                @input="
                    $emit('update:modelValue', $event.target.value)
                "
                class="field"
                :class="{ error: error }"
            />
            <p v-if="error" class="errorMessage">{{ error }}</p>
        </div>
    </div>
</template>

<style scoped>
.grid-container {
    display: grid;
    row-gap: 10px;
}

.inputContainer {
    position: relative;
}

.inputContainer::before {
    content: var(--content);
    margin-left: var(--isLeft);
    background-color: var(--error);
    color: var(--error-text);
    position: absolute;
    left: 1px;
    right: 1px;
    top: 0px;
    bottom: 1px;
    max-width: fit-content;
    height: 47px;
    padding-inline: 16px;
    margin-top: 1px;
    display: flex;
    align-items: center;
    border-radius: 4px;
    font-size: 18px;
    font-weight: var(--fw-bold);
    transition:
        background-color 0.3s ease,
        color 0.3s ease;
}

.inputContainer:hover::before {
    background-color: var(--primary-color);
    color: var(--slate900);
}

.inputContainer:focus-within::before {
    background-color: var(--primary-color);
    color: var(--slate900);
}

.label {
    color: var(--slate700);
    font-weight: var(--fw-medium);
}

.field {
    border: 1px solid var(--slate700);
    border-radius: 4px;
    padding: 10px;
    padding-left: var(--leftSpace);
    font-size: 18px;
    font-weight: var(--fw-bold);
    color: var(--slate900);
    width: 100%;
}

.field:focus,
.field:hover {
    border: 1px solid var(--primary-color);
    outline: unset;
}

.error {
    border: 1px solid var(--error-color);
}

.errorMessage {
    margin-top: 8px;
    font-size: 14px;
    color: var(--error-color);
}

/* Chrome, Safari, Edge, Opera */
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
}

/* Firefox */
input[type="number"] {
    -moz-appearance: textfield;
    appearance: textfield;
}
</style>
