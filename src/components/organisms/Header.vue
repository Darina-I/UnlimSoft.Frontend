<script setup>
import Link from '../atoms/Link.vue';
</script>

<template>
    <header class="flex flex-col items-center py-5 gap-4 border-b">
        <div class="text-2xl font-medium text-main">Тестовое задание на Frontend разработчика</div>
        <nav class="flex gap-4">
            <Link src="/" content="Главная страница" />
            <Link src="/groups" content="Формирование групп" />
        </nav>
    </header>
</template>