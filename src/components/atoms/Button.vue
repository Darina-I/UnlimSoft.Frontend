<script setup>
defineProps(['content', 'isDisabled'])

const emit = defineEmits(["click"]);

const handleClick = () => {
    emit('click')
}
</script>

<template>
    <button 
        class="bg-main rounded-lg text-white cursor-pointer px-4 py-1
            disabled:cursor-not-allowed disabled:bg-slate-400"
        :disabled="isDisabled"
        @click="handleClick"
    >
        {{ content }}
    </button>
</template>