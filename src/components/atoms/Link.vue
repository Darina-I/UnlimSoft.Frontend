<script setup>
defineProps(['src', 'content'])
</script>

<template>
    <RouterLink :to="src" class="text-lg border-b border-main">{{ content }}</RouterLink>
</template>