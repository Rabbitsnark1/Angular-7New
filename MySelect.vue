<template>
    <select :value="modelValue" @change="chageOption">
        <option disabled value="">Выберите из списка</option>
        <option
            v-for="option in option"
            :key="option.value"
            "value="option.value">
        {{option.name}}
        </option>
    </select>
</template>

<script>
export default {
    name: 'me-select',
    props: {
        modelValue: {
            type: String
        },
        options: {
            type: Array,
            default: () => []
        }
    },
    methods: {
        chageOption(event) {
            this.$emit('update:modelValue', event.target.value);
        }
    }
}
</script>

<style scoped>
</style>